<!-- showcase:hero:start -->
<p align="center">
  <img src="assets/img/app-icon.png" alt="GrillMate app icon" width="128">
</p>

# GrillMate

**Log cooks, grills & recipes.**

<p align="center">
  <a href="https://apps.apple.com/app/id6823652919">
    <img src="assets/img/app-store-badge.svg" alt="Download on the App Store" height="56">
  </a>
</p>

GrillMate is a **native iOS logbook for barbecue**: record every cooking session, keep track of your grills and smokers, save the recipes you want to make again, see how you cook through **stats and charts**, and keep everything in sync across your devices with **iCloud**. It's built with **SwiftUI for iOS 26**, in **light and dark mode**.

The goal is to **use every iOS feature Apple offers** where it fits a barbecue logbook: SwiftUI and Swift Charts, Core Data with CloudKit, **WidgetKit** Home Screen and Lock Screen widgets, Home Screen quick actions, alternate app icons, **local notifications** and **on-device Apple Intelligence** (Foundation Models) today, and Live Activities, the Dynamic Island, Control Center controls and AlarmKit next.

A personal project by **Andrea Stevanato**, designed, built and tested end to end. The app's source code is private: this README shows what the app does and explains how it's built.

<p align="center">
  <img src="assets/screenshots/cookings.png" alt="Cookings list" width="200">
  <img src="assets/screenshots/recipe-detail.png" alt="Recipe detail" width="200">
  <img src="assets/screenshots/stats.png" alt="Stats" width="200">
  <img src="assets/screenshots/settings.png" alt="Settings" width="200">
</p>
<!-- showcase:hero:end -->

Website: [grillmate.pages.dev](https://grillmate.pages.dev/)

<!-- showcase:toc:start -->
## 📑 Contents

- [👀 At a glance](#-at-a-glance)
- [🧰 Tech stack](#-tech-stack)
- [🧱 Architecture](#-architecture)
- [✨ Features](#-features)
  - [🍖 Cookings](#-cookings) · [🔥 Devices](#-devices) · [📖 Recipes](#-recipes) · [📊 Stats](#-stats)
  - [🔲 Widgets](#-widgets) · [⚡ Quick actions](#-quick-actions) · [📲 iPhone Duo](#-iphone-duo) · [🔄 iCloud sync](#-icloud-sync) · [🔔 Reminders](#-reminders) · [📤 Share your cooks](#-share-your-cooks) · [🔧 Settings and onboarding](#-settings-and-onboarding)
- [💡 Technical highlights](#-technical-highlights)
- [✅ Quality](#-quality)
- [🔭 What's next](#-whats-next)
<!-- showcase:toc:end -->

<!-- showcase:glance:start -->
## 👀 At a glance

| | |
|---|---|
| **What** | A complete iOS app: five tabs, add/edit forms with photos, charts, theming, **iCloud sync**, data export and import, **Home Screen and Lock Screen widgets**, **Home Screen quick actions**, weekly **reminders** with seasonal messages written by **on-device AI**, **iPhone Duo** support, onboarding, English and Italian |
| **Architecture** | **Modular**: one Swift package per feature plus shared core and UI packages, **MVVM** with repositories and **dependency injection** |
| **Modern Apple stack** | **Swift 6** strict concurrency, **SwiftUI**, Observation, **Swift Charts**, **WidgetKit**, **Core Data with CloudKit**, **Foundation Models**, **UserNotifications**, **Swift Testing** |
| **Engineering practice** | Generated Xcode project, lint in the build, unit tests per package, **Maestro UI tests**, **Xcode Cloud** tests on every push, **fastlane** TestFlight uploads, Conventional Commits, gitflow and a changelog |
<!-- showcase:glance:end -->

<!-- showcase:stack:start -->
## 🧰 Tech stack

| Area | Choice |
|------|--------|
| Platform | **iOS 26+**, Xcode 26 |
| Language | **Swift 6** with strict concurrency; main-actor default isolation and approachable concurrency in the app target |
| UI | **SwiftUI**, Observation (`@Observable`), **Swift Charts** |
| Persistence | **Core Data**, used as a Codable store, mirrored to the user's private iCloud database with **CloudKit** |
| On-device AI | **Foundation Models** with guided generation, falling back to hand-written messages where Apple Intelligence isn't available |
| Notifications | **UserNotifications**, local only: no server, no push |
| Dependency injection | [Factory](https://github.com/hmlongco/Factory), behind a thin in-house wrapper |
| Modularization | Local **Swift packages**: one per feature plus two shared ones |
| Project generation | [Tuist](https://tuist.dev) |
| Lint | [SwiftLint](https://github.com/realm/SwiftLint), run as a pre-build phase, with custom rules |
| Tests | **Swift Testing** for unit tests, [Maestro](https://maestro.dev) for UI integration tests |
| Continuous integration | [Xcode Cloud](https://developer.apple.com/xcode-cloud/): every push to `develop` runs the unit tests of all packages |
| Release automation | [fastlane](https://fastlane.tools): tests, TestFlight uploads and the App Store listing |
| Localization | String catalogs (`.xcstrings`), English and Italian |

Factory is the only third-party runtime dependency.
<!-- showcase:stack:end -->

<!-- showcase:architecture:start -->
## 🧱 Architecture

The app target is a **thin shell**: it owns the `TabView`, the scene delegate for quick actions and the deep-link routing, and wires each tab to a feature package. A **widget extension** reads only the shared core and UI packages. Everything else lives in local Swift packages.

```mermaid
graph TD
    App[GrillMate app target]
    App --> Cookings[CookingsFeature]
    App --> Barbecue[BarbecueFeature]
    App --> Recipes[RecipesFeature]
    App --> Stats[StatsFeature]
    App --> Settings[SettingsFeature]
    App --> Onboarding[OnboardingFeature]
    Widgets[GrillMateWidgets extension]

    Cookings & Barbecue & Recipes & Stats & Settings & Onboarding --> CoreUI["GrillMateCoreUI<br/>design system, components, Router, ViewState"]
    Cookings & Barbecue & Recipes & Stats & Settings & Onboarding --> Core["GrillMateCore<br/>domain, repositories, Core Data, DI"]
    Widgets --> CoreUI & Core
    Core --> Factory[Factory]
```

- **Features never depend on each other.** Each one depends only on `GrillMateCore` and `GrillMateCoreUI`, so it builds, tests and previews on its own.
- **`GrillMateCoreUI` doesn't depend on `GrillMateCore`.** Shared components take plain, already formatted values, and each feature maps its domain models to them.
- **One public entry point per feature**: a `@MainActor` factory enum that returns the feature's navigation stack. Screens, view models and routes stay internal to the package.

### 🔀 Data flow

```mermaid
flowchart LR
    View --> ViewModel
    ViewModel --> Protocol[Repository protocol]
    Protocol --> Repository
    Repository --> DataSource[Local data source]
    DataSource --> CoreData[(Core Data)]
```

View models are `@Observable` main-actor classes that expose a `ViewState<T>` (idle, loading, loaded, error) and resolve their repositories from the DI container. Only the **repository protocols are public**: data sources and the Core Data stack are internal to `GrillMateCore` and do their work on **background contexts**.
<!-- showcase:architecture:end -->

<!-- showcase:features:start -->
## ✨ Features

Every screenshot is taken from the app running on demo data, in **light and dark mode** side by side.

### 🍖 Cookings
Log every session and follow it from **planned** to **completed**.

<p align="center">
  <img src="assets/screenshots/cookings.png" alt="Cookings list grouped by month (light)" width="200">
  <img src="assets/screenshots/cookings-dark.png" alt="Cookings list grouped by month (dark)" width="200">
  <img src="assets/screenshots/cooking-detail.png" alt="Cooking detail with a stretchy photo header (light)" width="200">
  <img src="assets/screenshots/cooking-detail-dark.png" alt="Cooking detail with a stretchy photo header (dark)" width="200">
</p>

- Title, barbecue, **status** (planned, in progress, completed), proteins and technique
- Start and end time, **target, reached and ambient temperatures**, notes
- Up to **3 photos** per cook, from the camera or the photo library
- A list **grouped by month**, with photo banners and **swipe to complete**

### 🔥 Devices
All your grills, smokers, kamados and planchas in one place.

<p align="center">
  <img src="assets/screenshots/devices.png" alt="Devices list (light)" width="260">
  <img src="assets/screenshots/devices-dark.png" alt="Devices list (dark)" width="260">
</p>

- **Manufacturer** picked from a long list of brands (or "Other"), plus model and description
- Type, **fuel**, grilling area, burners, portability, purchase date and notes

### 📖 Recipes
Save the recipes you want to make again.

<p align="center">
  <img src="assets/screenshots/recipes.png" alt="Recipe cards (light)" width="200">
  <img src="assets/screenshots/recipes-dark.png" alt="Recipe cards (dark)" width="200">
  <img src="assets/screenshots/recipe-detail.png" alt="Recipe detail with a photo gallery (light)" width="200">
  <img src="assets/screenshots/recipe-detail-dark.png" alt="Recipe detail with a photo gallery (dark)" width="200">
</p>

- Name, summary, technique, **prep and cook time**, servings and **target core temperature**
- **Ingredients** with quantity and unit, **ordered steps**, notes and up to 3 photos
- A swipeable full-width **photo gallery** in the detail

### 📊 Stats
See how you cook, with **Swift Charts**.

<p align="center">
  <img src="assets/screenshots/stats.png" alt="Stats for this year (light)" width="200">
  <img src="assets/screenshots/stats-dark.png" alt="Stats for this year (dark)" width="200">
  <img src="assets/screenshots/stats-ocean.png" alt="Stats with the Ocean accent color (light)" width="200">
  <img src="assets/screenshots/stats-ocean-dark.png" alt="Stats with the Ocean accent color (dark)" width="200">
</p>

- This month's cooks, **total, average and longest cooking time**, and a chart of the proteins cooked
- **Extended stats**: other periods, time per barbecue, techniques, barbecue types, cooks per month, core temperature accuracy and busiest day
- Charts follow the **accent color** chosen in Settings (last two: the Ocean theme)

### 🔲 Widgets
Your cook at a glance, on the **Home Screen** and the **Lock Screen**.

<p align="center">
  <img src="assets/screenshots/widget-now-cooking.png" alt="Now cooking widget with a live elapsed timer (light)" width="180">
  <img src="assets/screenshots/widget-now-cooking-dark.png" alt="Now cooking widget with a live elapsed timer (dark)" width="180">
  <img src="assets/screenshots/widget-grill-stats.png" alt="Grill stats widget with this month's cookings and grill time (light)" width="180">
  <img src="assets/screenshots/widget-grill-stats-dark.png" alt="Grill stats widget with this month's cookings and grill time (dark)" width="180">
</p>

- **Now cooking** (small, and rectangular on the Lock Screen): the cook in progress with a **live elapsed timer** and its target temperature, otherwise the next planned cook, otherwise a "Fire it up" shortcut
- **Grill stats** (small, and circular on the Lock Screen): this month's **cookings and grill time**
- Both use the app's **accent color**, **open the right screen** when tapped, support the tinted and clear Home Screen styles, and come in English and Italian

### ⚡ Quick actions
Long-press the app icon to jump straight into the app.

<p align="center">
  <img src="assets/screenshots/quick-actions.png" alt="Home Screen quick actions menu (light)" width="260">
  <img src="assets/screenshots/quick-actions-dark.png" alt="Home Screen quick actions menu (dark)" width="260">
</p>

- **New cooking**, **New recipe**, **Stats** and **Add device**, one tap from the Home Screen
- The **cooking in progress**, or the next planned one, comes first and opens directly, so you can update a cook while it's on the grill
- The menu is **dynamic**: it's refreshed every time you leave the app, so it always shows your current cook, in English or Italian

### 📲 iPhone Duo
GrillMate runs on **both displays** of the foldable iPhone Duo.

<p align="center">
  <img src="assets/screenshots/duo-outer.png" alt="Cookings on the iPhone Duo outer display, with the tabs in the vertical bar (light)" height="380">
  <img src="assets/screenshots/duo-outer-dark.png" alt="Cookings on the iPhone Duo outer display, with the tabs in the vertical bar (dark)" height="380">
</p>
<p align="center">
  <img src="assets/screenshots/duo-inner.png" alt="Recipe detail on the unfolded iPhone Duo inner display (light)" width="420">
  <img src="assets/screenshots/duo-inner-dark.png" alt="Recipe detail on the unfolded iPhone Duo inner display (dark)" width="420">
</p>

- On the **outer display**, the tabs move to the **vertical system bar** beside the content
- **Unfold** the phone and the screen you're on carries over to the large **inner display**, in landscape
- The App Store listing has captioned screenshots for both displays, in English and Italian

### 🔄 iCloud sync
Your logbook on every device.

- Barbecues, cookings, recipes and **photos** sync across your iPhones through your **private iCloud database**
- One toggle in Settings turns sync **on or off immediately**, without restarting the app
- Open lists **refresh by themselves** when changes arrive from another device
- **Reinstall and your data comes back**: the sync choice is remembered in iCloud, so a fresh install turns sync back on by itself and restores everything
- **Export** everything, photos included, to a single file, after a confirmation that shows how many items it will contain, and **import** it back to restore your data on any iPhone

### 🔔 Reminders
A nudge to fire up the grill, once or twice a week.

<p align="center">
  <img src="assets/screenshots/reminder.png" alt="A GrillMate reminder banner on the Home Screen: Spooky smoke, a Halloween menu idea (light)" width="260">
  <img src="assets/screenshots/reminder-dark.png" alt="A GrillMate reminder banner on the Home Screen: Spooky smoke, a Halloween menu idea (dark)" width="260">
</p>

- **Thursday evening** to plan the weekend cook and **Saturday morning** to fire it up today
- Messages follow the **moment of the year**, with a recipe idea: Easter week and Easter Monday, Ferragosto, Halloween, Christmas, New Year and the four seasons
- On iPhones with **Apple Intelligence**, the message is **written on the device** from what you cook: your favourite cuts and techniques, your barbecue, how long since your last cook
- **Reminders for a planned cook**: the evening before (18:00) and the morning of the day (10:00), opening that cooking
- **Never pushy**: the weekly nudges are replaced only by a planned cook's reminders, and logged cooks skip nothing
- Messages are written in the app's language, and fall back to a hand-written one if the model slips
- Asked for **only after you add a cooking**, never at first launch, and turned on or off in Settings
- Tapping a reminder opens a **new cooking**

### 📤 Share your cooks
Turn a cook into a card for your Stories or feed.

<p align="center">
  <img src="assets/screenshots/share-card.png" alt="The Share card screen with a Story 9:16 preview and five color templates (light)" width="260">
  <img src="assets/screenshots/share-card-dark.png" alt="The Share card screen with a Story 9:16 preview and five color templates (dark)" width="260">
</p>

- A **Share** button on every saved cooking opens a **live preview** of the card
- **Story 9:16** or **Feed 4:5** ratio, with **five colour templates** taken from the app icons (Classic, Ember, Ocean, Midnight, Kamado)
- Rendered in full resolution (1080 px wide) and shared through the **system share sheet**: WhatsApp, Messages, Instagram or Save Image
- A small GrillMate watermark on the card

### 🔧 Settings and onboarding

<p align="center">
  <img src="assets/screenshots/settings.png" alt="Settings with the Reminders toggle and the Data section (light)" width="200">
  <img src="assets/screenshots/settings-dark.png" alt="Settings with the Reminders toggle and the Data section (dark)" width="200">
  <img src="assets/screenshots/onboarding.png" alt="Onboarding tour (light)" width="200">
  <img src="assets/screenshots/onboarding-dark.png" alt="Onboarding tour (dark)" width="200">
</p>

- **Light, dark or system appearance**, **six accent colors** and **ten app icons**, including Pride, Italy and USA
- A **Reminders** toggle, which points to iOS Settings when notifications are blocked
- A first-launch **onboarding tour** that can be replayed, now **eight pages**, including the Home Screen **widgets**, the weekly **reminders** and **sharing a cook**
- Privacy policy, terms of use and release notes open **inside the app**, in a Safari sheet, in the app's language
- Full-screen **photo viewer** with paging and pinch to zoom
- **English and Italian**
<!-- showcase:features:end -->

<!-- showcase:highlights:start -->
## 💡 Technical highlights

### 🧭 Navigation as data
Each feature owns a generic, observable `Router<Route>` bound to `NavigationStack(path:)`, with an enum of routes. Screens never touch the router: they receive closures such as `onAddTapped` or `onCookingTapped`, so they stay **reusable and easy to preview**.

### ⚡ Quick actions as deep links
Every quick action is a **deep link** on a custom URL scheme, parsed into a typed `DeepLink` in `GrillMateCore`. The root view picks the tab, and each feature's navigation stack turns the link into its own routes, so a quick action, a widget and any future notification opens the right screen **without features knowing about each other**. The shortcut items are rebuilt from the current data whenever the app goes to the background.

### 🔲 Widgets that never open the store
The widget extension **never touches Core Data or CloudKit**: opening the store from a second process would race with the app and with iCloud mirroring. Instead the app writes a small JSON **snapshot** of what the widgets show into a shared **App Group** container, at launch, when it goes to the background, when data changes and when the theme changes, and reloads the timelines **only when the snapshot changed**, since iOS budgets reloads. The timer is drawn by the system, and a second timeline entry resets the monthly stats when a new month starts.

### 💾 Core Data as a Codable blob store
Every persisted entity has the same three attributes: an id, a **JSON-encoded payload** of the domain model and an update date. Domain models stay plain `Codable` Swift structs, and adding a persisted type takes one entity plus a small data source on top of generic fetch and upsert helpers. Models that change shape keep **decoding their legacy payloads**, with tests that decode the old JSON. The schema follows **CloudKit's rules** (no uniqueness constraints, every attribute optional), enforced by a test, and another test **pins the model deployed to production CloudKit**, so a model change can't ship without deploying the schema first. Photos live in **their own entities, indexed by owner**, so list fetches never decode image data, and are **downsampled with ImageIO** before being stored.

### 🔄 iCloud sync that switches live
A single shared Core Data stack can **reload its store** with or without `NSPersistentCloudKitContainer` on the same file, so turning sync on or off applies immediately. Without uniqueness constraints, reads keep only the newest copy of an id, deletes run in a context so they're mirrored, and remote changes, **coalesced to one per second**, tell the open lists to reload. The sync choice is also saved in **iCloud key-value storage**, which survives deleting the app, so a fresh install silently turns sync back on and the data flows back in. Export writes one **versioned JSON file** with every item and photo; import **validates the whole file** before touching the store and rejects files from newer app versions.

### 🎨 Design tokens and a themable accent
Colors, fonts and SF Symbols are read through `Design` **tokens**; features never hard-code a symbol name. The accent color is an **environment value** set once at the root, so changing the theme recolors controls, cards and Swift Charts palettes in one place. The ten **alternate app icons** are generated from the master artwork, and the icon change goes through a **small protocol over the system API**, so it's unit-tested without UIKit.

### 🧠 On-device AI with a safe fallback
Reminder messages are written by Apple's **on-device model** (Foundation Models) with **guided generation**: the model fills a typed title and body, which are checked against notification length limits. The prompt grounds it in the moment of the year, a hand-written example and the user's own cooks, and **nothing leaves the device**. Where Apple Intelligence isn't available, or a reply is rejected, a **hand-written catalogue** in English and Italian takes over. The model runs only while the app is active, is **cancelled when it goes to the background**, and its messages are kept per notification until the user's data or language changes.

### 🔔 Notifications that respect the user
The next four weeks are scheduled as **one-off local notifications**, skipping three days around any cook, in the **Gregorian calendar** whatever the region's calendar. A **singleton actor** runs reschedules one at a time, a stop request reaches even a pass still queued, and the background pass gets **extra time from iOS** to finish. Permission is asked **after the user adds a cooking**, the moment a reminder makes sense, not at first launch.

### 🧩 Shared UI, different data
Cards, form fields, photo galleries and the stretchy photo header live in `GrillMateCoreUI`. The onboarding, the lists and the stats render **the same components**: the onboarding feeds them sample strings, the lists feed them domain data.

### 🧪 Demo data and a dev app
A separate **development app variant** installs next to the release one, so testing never touches the real install. Two extra schemes launch the app on an **in-memory store** filled with realistic data (barbecues, recipes and a year of cookings dated relative to today). They're used for screenshots, demos, **UI tests** and manual testing, **never touch the real store**, and are compiled only in Debug. The development app also adds a **Debug overlay**, compiled only into it, that sends a test reminder on demand, written by the model or taken from the catalogue.

### 🌍 Localized per package
Each package ships its **own string catalog** in English and Italian. A **custom SwiftLint rule** fails the build when a view in a package uses a bare string literal, which would be looked up in the app's catalog and never translated.
<!-- showcase:highlights:end -->

<!-- showcase:quality:start -->
## ✅ Quality

- **Tests**: more than **200 Swift Testing tests** across the app, core, UI and feature packages, including the reminder schedule across daylight saving and region calendars, overlapping reschedules, quick-action building, the widget snapshot, an export and import round trip, restoring iCloud sync after a reinstall, a check that the Core Data model stays CloudKit-compatible, a pin of the production CloudKit schema and decoding tests for legacy payloads.
- **UI tests**: black-box **Maestro** flows drive the real app on a simulator, starting from a clean install on the demo data, so every run is deterministic. They select views by **accessibility identifier**, defined in one place in the shared UI package, never by text, so they pass in English and Italian. One script builds the app, installs it and runs the flows with a JUnit report. See [UI tests with Maestro](#-ui-tests-with-maestro).
- **Lint**: **SwiftLint runs before every build**, with rules such as no force unwrapping, implicit returns and closure length limits, plus custom project rules.
- **Generated project**: the Xcode project and workspace are generated from a single **Tuist** manifest.
- **Continuous integration**: **Xcode Cloud** builds and tests every push to `develop` with one test plan that covers the unit tests of the app, the shared packages and every feature, so a merged pull request is checked without a local run.
- **Releases**: **fastlane** lanes run the tests, archive and upload builds to **TestFlight**, and push the App Store listing after checking every field against Apple's limits. The app ships a **privacy manifest**: no tracking, no data collected.
- **Process**: **Conventional Commits**, **gitflow** with pull requests into `develop`, and a changelog following Keep a Changelog.

### 🎬 UI tests with Maestro
The first suite covers the **onboarding**: finishing it page by page, skipping it, swiping between pages, and checking that it isn't shown again on the next launch. Below, the "finish" flow runs on the iPhone 17 simulator: the steps on the left, the app on the right.

<p align="center">
  <img src="assets/screenshots/onboarding-ui-test.gif" alt="Maestro running the onboarding flow: each step turns green as the app moves through the six onboarding pages and lands on the Cookings tab" width="720">
</p>

An excerpt of that flow:

```yaml
# Goes through every page with "Continue" and finishes on "Get started".
- runFlow: ../../subflows/launch_fresh.yaml   # clean install on the demo data
- tapOn:
    id: onboarding.primary                     # "Continue"
- assertVisible:
    id: onboarding.page.cookings
# … devices, recipes, stats, widgets
- assertVisible:
    id: onboarding.skip
    enabled: false                             # "Skip" can't be tapped on the last page
- tapOn:
    id: onboarding.primary                     # "Get started"
- assertVisible:
    id: cookings.add                           # landed on the Cookings tab
```
<!-- showcase:quality:end -->

<!-- showcase:next:start -->
## 🔭 What's next

- Live Activity and Dynamic Island for the cook in progress
- Control Center controls
- Cook alarms
- AI-assisted cooking features
- Crash reporting and analytics
- Automated App Store screenshots and CI releases
<!-- showcase:next:end -->
