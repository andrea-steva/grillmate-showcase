<!-- showcase:hero:start -->
<p align="center">
  <img src="assets/img/app-icon.png" alt="GrillMate app icon" width="128">
</p>

# GrillMate

**Log cooks, grills & recipes.**

> [!IMPORTANT]
> 🚧 **Work in progress.** GrillMate is in active development and **will be available on the App Store soon**.

GrillMate is a **native iOS logbook for barbecue**: record every cooking session, keep track of your grills and smokers, save the recipes you want to make again, see how you cook through **stats and charts**, and keep everything in sync across your devices with **iCloud**. It's built with **SwiftUI for iOS 26**.

The goal is to **use every iOS feature Apple offers** where it fits a barbecue logbook: SwiftUI and Swift Charts, Core Data with CloudKit, Home Screen quick actions and alternate app icons today, and Widgets, Live Activities, the Dynamic Island, Control Center controls and AlarmKit next.

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
- [✨ Features](#-features)
  - [🍖 Cookings](#-cookings) · [🔥 Devices](#-devices) · [📖 Recipes](#-recipes) · [📊 Stats](#-stats)
  - [⚡ Quick actions](#-quick-actions) · [🔄 iCloud sync](#-icloud-sync) · [🔧 Settings and onboarding](#-settings-and-onboarding)
- [🧰 Tech stack](#-tech-stack)
- [🧱 Architecture](#-architecture)
- [💡 Technical highlights](#-technical-highlights)
- [✅ Quality](#-quality)
- [🔭 What's next](#-whats-next)
<!-- showcase:toc:end -->

<!-- showcase:glance:start -->
## 👀 At a glance

| | |
|---|---|
| **What** | A complete iOS app: five tabs, add/edit forms with photos, charts, theming, **iCloud sync**, data export and import, **Home Screen quick actions**, onboarding, English and Italian |
| **Architecture** | **Modular**: one Swift package per feature plus shared core and UI packages, **MVVM** with repositories and **dependency injection** |
| **Modern Apple stack** | **Swift 6** strict concurrency, **SwiftUI**, Observation, **Swift Charts**, **Core Data with CloudKit**, **Swift Testing** |
| **Engineering practice** | Generated Xcode project, lint in the build, unit tests per package, **fastlane** TestFlight uploads, Conventional Commits, gitflow and a changelog |
<!-- showcase:glance:end -->

<!-- showcase:features:start -->
## ✨ Features

Every screenshot is taken from the app running on demo data.

### 🍖 Cookings
Log every session and follow it from **planned** to **completed**.

<p align="center">
  <img src="assets/screenshots/cookings.png" alt="Cookings list grouped by month" width="260">
  <img src="assets/screenshots/cooking-detail.png" alt="Cooking detail with a stretchy photo header" width="260">
</p>

- Title, barbecue, **status** (planned, in progress, completed), proteins and technique
- Start and end time, **target, reached and ambient temperatures**, notes
- Up to **3 photos** per cook, from the camera or the photo library
- A list **grouped by month**, with photo banners and **swipe to complete**

### 🔥 Devices
All your grills, smokers, kamados and planchas in one place.

<p align="center">
  <img src="assets/screenshots/devices.png" alt="Devices list" width="260">
</p>

- **Manufacturer** picked from a long list of brands (or "Other"), plus model and description
- Type, **fuel**, grilling area, burners, portability, purchase date and notes

### 📖 Recipes
Save the recipes you want to make again.

<p align="center">
  <img src="assets/screenshots/recipes.png" alt="Recipe cards" width="260">
  <img src="assets/screenshots/recipe-detail.png" alt="Recipe detail with a photo gallery" width="260">
</p>

- Name, summary, technique, **prep and cook time**, servings and **target core temperature**
- **Ingredients** with quantity and unit, **ordered steps**, notes and up to 3 photos
- A swipeable full-width **photo gallery** in the detail

### 📊 Stats
See how you cook, with **Swift Charts**.

<p align="center">
  <img src="assets/screenshots/stats.png" alt="Stats for this year" width="260">
  <img src="assets/screenshots/stats-ocean.png" alt="Stats with the Ocean accent color" width="260">
</p>

- This month's cooks, **total, average and longest cooking time**, and a chart of the proteins cooked
- **Extended stats (PRO)**: other periods, time per barbecue, techniques, barbecue types, cooks per month, core temperature accuracy and busiest day
- Charts follow the **accent color** chosen in Settings (right: the Ocean theme)

### ⚡ Quick actions
Long-press the app icon to jump straight into the app.

<p align="center">
  <img src="assets/screenshots/quick-actions.png" alt="Home Screen quick actions menu" width="260">
</p>

- **New cooking**, **New recipe**, **Stats** and **Add device**, one tap from the Home Screen
- The **cooking in progress**, or the next planned one, comes first and opens directly, so you can update a cook while it's on the grill
- The menu is **dynamic**: it's refreshed every time you leave the app, so it always shows your current cook, in English or Italian

### 🔄 iCloud sync
Your logbook on every device.

- Barbecues, cookings, recipes and **photos** sync across your iPhones through your **private iCloud database** (PRO)
- One toggle in Settings turns sync **on or off immediately**, without restarting the app
- Open lists **refresh by themselves** when changes arrive from another device
- **Reinstall and your data comes back**: the sync choice is remembered in iCloud, so a fresh install turns sync back on by itself and restores everything
- **Export** everything, photos included, to a single file, after a confirmation that shows how many items it will contain, and **import** it back to restore your data on any iPhone

### 🔧 Settings and onboarding

<p align="center">
  <img src="assets/screenshots/settings.png" alt="Settings with the Data section" width="260">
  <img src="assets/screenshots/onboarding.png" alt="Onboarding tour" width="260">
</p>

- Light, dark or system appearance, **six accent colors** and **ten app icons**, including Pride, Italy and USA (PRO)
- A first-launch **onboarding tour** that can be replayed
- Privacy policy, terms of use and release notes open on the GrillMate website, in the app's language
- Full-screen **photo viewer** with paging and pinch to zoom
- **English and Italian**
<!-- showcase:features:end -->

<!-- showcase:stack:start -->
## 🧰 Tech stack

| Area | Choice |
|------|--------|
| Platform | **iOS 26+**, Xcode 26 |
| Language | **Swift 6** with strict concurrency; main-actor default isolation and approachable concurrency in the app target |
| UI | **SwiftUI**, Observation (`@Observable`), **Swift Charts** |
| Persistence | **Core Data**, used as a Codable store, mirrored to the user's private iCloud database with **CloudKit** |
| Dependency injection | [Factory](https://github.com/hmlongco/Factory), behind a thin in-house wrapper |
| Modularization | Local **Swift packages**: one per feature plus two shared ones |
| Project generation | [Tuist](https://tuist.dev) |
| Lint | [SwiftLint](https://github.com/realm/SwiftLint), run as a pre-build phase, with custom rules |
| Tests | **Swift Testing** |
| Release automation | [fastlane](https://fastlane.tools): tests, TestFlight uploads and the App Store listing |
| Localization | String catalogs (`.xcstrings`), English and Italian |

Factory is the only third-party runtime dependency.
<!-- showcase:stack:end -->

<!-- showcase:architecture:start -->
## 🧱 Architecture

The app target is a **thin shell**: it owns the `TabView`, the scene delegate for quick actions and the deep-link routing, and wires each tab to a feature package. Everything else lives in local Swift packages.

```mermaid
graph TD
    App[GrillMate app target]
    App --> Cookings[CookingsFeature]
    App --> Barbecue[BarbecueFeature]
    App --> Recipes[RecipesFeature]
    App --> Stats[StatsFeature]
    App --> Settings[SettingsFeature]
    App --> Onboarding[OnboardingFeature]
    App --> Profile[ProfileFeature]

    Cookings & Barbecue & Recipes & Stats & Settings & Onboarding & Profile --> CoreUI["GrillMateCoreUI<br/>design system, components, Router, ViewState"]
    Cookings & Barbecue & Recipes & Stats & Settings & Onboarding & Profile --> Core["GrillMateCore<br/>domain, repositories, Core Data, DI"]
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

<!-- showcase:highlights:start -->
## 💡 Technical highlights

### 🧭 Navigation as data
Each feature owns a generic, observable `Router<Route>` bound to `NavigationStack(path:)`, with an enum of routes. Screens never touch the router: they receive closures such as `onAddTapped` or `onCookingTapped`, so they stay **reusable and easy to preview**.

### ⚡ Quick actions as deep links
Every quick action is a **deep link** on a custom URL scheme, parsed into a typed `DeepLink` in `GrillMateCore`. The root view picks the tab, and each feature's navigation stack turns the link into its own routes, so a quick action, and any future widget or notification, opens the right screen **without features knowing about each other**. The shortcut items are rebuilt from the current data whenever the app goes to the background.

### 💾 Core Data as a Codable blob store
Every persisted entity has the same three attributes: an id, a **JSON-encoded payload** of the domain model and an update date. Domain models stay plain `Codable` Swift structs, and adding a persisted type takes one entity plus a small data source on top of generic fetch and upsert helpers. Models that change shape keep **decoding their legacy payloads**, with tests that decode the old JSON. The schema follows **CloudKit's rules** (no uniqueness constraints, every attribute optional), enforced by a test, and another test **pins the model deployed to production CloudKit**, so a model change can't ship without deploying the schema first.

### 🔄 iCloud sync that switches live
A single shared Core Data stack can **reload its store** with or without `NSPersistentCloudKitContainer` on the same file, so turning sync on or off applies immediately. Without uniqueness constraints, reads keep only the newest copy of an id, deletes run in a context so they're mirrored, and remote changes, **coalesced to one per second**, tell the open lists to reload. The sync choice is also saved in **iCloud key-value storage**, which survives deleting the app, so a fresh install silently turns sync back on and the data flows back in.

### 📦 A portable export format
Export writes one **versioned JSON file** with every item and photo. Import decodes and **validates the whole file** before touching the store, rejects files from newer app versions, and replaces the data only after a confirmation that shows what the file contains.

### 📸 Photos out of the hot path
Cooking and recipe photos live in **their own entities, indexed by owner**, rather than inside the parent payload, so list fetches never decode image data. Photos are **downsampled with ImageIO** before being stored and are deleted together with their owner.

### 🎨 Design tokens and a themable accent
Colors, fonts and SF Symbols are read through `Design` **tokens**; features never hard-code a symbol name. The accent color is an **environment value** set once at the root, so changing the theme recolors controls, cards and Swift Charts palettes in one place.

### 📱 Alternate app icons
Ten home screen icons are **generated from the master artwork**, so each variant keeps the exact flame and grill. The picker shows small previews bundled with the Settings feature, because an app icon set can't be loaded as an image, and the icon change goes through a **small protocol over the system API**, so the logic is unit-tested without UIKit. Alternate icons are PRO: free users see them locked, but **the icon in use is never locked**, so nobody loses the icon they already set.

### 🧩 Shared UI, different data
Cards, form fields, photo galleries and the stretchy photo header live in `GrillMateCoreUI`. The onboarding, the lists and the stats render **the same components**: the onboarding feeds them sample strings, the lists feed them domain data.

### 💎 Free and PRO behind one protocol
PRO status and the free-tier limits are read from a **single entitlement repository**, and the limits are configured in one place. Features ask `isPro()` and never know where the answer comes from.

### 🧪 Demo data schemes
Two extra schemes launch the app on an **in-memory store** filled with realistic data (barbecues, recipes and a year of cookings dated relative to today), one as a free user and one as PRO. They're used for screenshots, demos and manual testing, **never touch the real store**, and are compiled only in Debug.

### 🌍 Localized per package
Each package ships its **own string catalog** in English and Italian. A **custom SwiftLint rule** fails the build when a view in a package uses a bare string literal, which would be looked up in the app's catalog and never translated.
<!-- showcase:highlights:end -->

<!-- showcase:quality:start -->
## ✅ Quality

- **Tests**: about **100 Swift Testing tests** across the app, core, UI and feature packages, including quick-action building, an export and import round trip, restoring iCloud sync after a reinstall, a check that the Core Data model stays CloudKit-compatible, a pin of the production CloudKit schema and decoding tests for legacy payloads.
- **Lint**: **SwiftLint runs before every build**, with rules such as no force unwrapping, implicit returns and closure length limits, plus custom project rules.
- **Generated project**: the Xcode project and workspace are generated from a single **Tuist** manifest.
- **Releases**: **fastlane** lanes run the tests, archive and upload builds to **TestFlight**, and push the App Store listing after checking every field against Apple's limits. The app ships a **privacy manifest**: no tracking, no data collected.
- **Process**: **Conventional Commits**, **gitflow** with pull requests into `develop`, and a changelog following Keep a Changelog.
<!-- showcase:quality:end -->

<!-- showcase:next:start -->
## 🔭 What's next

- PRO version with in-app purchase
- Widgets
- Live Activity and Dynamic Island for the cook in progress
- Control Center controls
- Cook alarms
- AI-assisted cooking features
- Engagement notifications
- Crash reporting and analytics
- Automated App Store screenshots and CI releases
<!-- showcase:next:end -->
