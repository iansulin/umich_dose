## Overview of `MainView.swift`

`MainView.swift` is the primary SwiftUI view for the Dose Watch App on Apple Watch, managing core user interactions such as displaying HealthKit status, handling user identification, triggering health data uploads, managing notifications, and presenting surveys. It includes a crucial testing mode flag that enables or disables developer-only debug features like viewing and clearing scheduled notifications, allowing seamless transitions between development and production environments. The view responds to app lifecycle changes, user actions, and notification events to provide a smooth and responsive experience tailored for both study participants and developers.

---

### How the app interface looks and behaves

The interface features a clean, vertically stacked layout with well-spaced sections optimized for the Apple Watch’s small screen. It includes a subtle progress indicator paired with dynamic status text for background HealthKit syncing, and prominent buttons for entering the user ID and uploading data. Developer tools, such as buttons to preview or clear notifications, are shown only when testing mode is enabled. Modal sheets are used for user ID input, survey responses, and viewing scheduled notifications, offering a minimal yet functional design tailored for quick, glanceable interactions.

- **Layout:** The main content is inside a vertical `ScrollView` with a `VStack` stacking components with some spacing, allowing scrolling if content overflows.

- **HealthKit Status:** At the top, there is a small section showing a spinner (`ProgressView`) and a status message text that updates dynamically based on the HealthKit syncing state (e.g., “fetching health data…”, “Uploading data…”). The text is green, subtle but visible.

- **User ID Display:** Below that, a large button labeled “User ID” shows the current stored user ID or “None” if empty. Tapping it brings up a modal sheet to enter or edit the participant’s ID.

- **Upload Button:** A prominent button labeled “Upload” triggers HealthKit permission requests and data upload. It disables itself during active upload operations and changes color to indicate availability.

- **Alerts:** Alerts pop up when required (e.g., no user ID entered, missing HealthKit permissions), ensuring user feedback and error handling.

- **Testing Mode Buttons (optional):** If testing mode is enabled, additional buttons appear for developers to “Show Scheduled” notifications and “Clear Notifications” — these are displayed in a simple vertical list styled consistently with the rest.

- **Surveys:** Survey sheets appear modally when triggered by notifications, showing different random survey views.

- **Navigation:** The view uses `NavigationView` but hides the navigation bar, focusing on full-screen content.

---

### Testing Mode Flag

- The `isTestingMode` constant acts as a feature toggle specifically for development and debugging purposes.
- When set to `1`, it enables additional UI elements and functionality intended to help developers and testers verify notification scheduling and management. For example:
  - Displays buttons for viewing all currently scheduled local notifications.
  - Provides a quick way to clear all pending and delivered notifications, allowing repeated testing without residual state.
- When set to `0` (the default/production setting), these debug buttons are hidden to present a clean, user-focused interface.
- This approach helps ensure that testing tools do not accidentally appear in production, while still being readily available during development.
```swift
    // ============================================================================================= //
    // Testing mode flag: 1 = show debug buttons; 0 = hide debug buttons
    let isTestingMode = 0
    // ============================================================================================= //
```

#### How Researchers or Developers Can Use It

- **Enable testing features during development:**  
  Setting `isTestingMode = 1` unlocks extra debug UI controls such as:  
  - Viewing scheduled notifications  
  - Clearing all notifications  
  - Other developer tools to facilitate testing

- **Facilitate easier debugging and validation:**  
  This helps verify that notifications and other app features behave correctly without leftover data from previous tests.

- **Disable debug UI in production:**  
  Setting `isTestingMode = 0` hides these debug controls to ensure a clean, user-friendly interface for study participants or general users.

#### Why Do We Need the Testing Mode Flag?

- **Separation of concerns:**  
  Allows a single codebase to support both production and testing environments without separate builds or branches.

- **Safety:**  
  Prevents exposing potentially destructive or confusing debug tools to real users.

- **Control:**  
  Offers a simple way to toggle developer features on or off by changing one constant.

- **Flexibility:**  
  Supports iterative testing without needing to remove debug code or rely on complex configurations.

---

### Additional Key Components

#### Environment & Observed Objects
- `@Environment(\.scenePhase)` — Monitors app lifecycle state (active, background, inactive).  
- `@EnvironmentObject` references:  
  - `notificationManager` — Manages local notifications.  
  - `healthKitManager` — Manages HealthKit authorization and data syncing.  
- `@ObservedObject surveyManager` — Controls survey-related state and presentation.

#### State Variables
- Various `@State` properties control UI elements and internal logic, including user ID storage, view appearance flags, alert presentation, survey sheet visibility, and pending notifications.

#### HealthKit Status Display
- Displays a progress indicator and status text reflecting the current state of HealthKit data operations (authorizing, fetching, uploading, etc.).

#### User ID Display and Editing
- Shows the current User ID (or "None" if not set).  
- Tapping the User ID button presents a `ParticipantView` sheet to input or edit the ID.  
- Disables editing once a User ID is set.

#### HealthKit Upload Button
- Triggers HealthKit authorization and data fetching/upload.  
- Checks that a User ID is set before proceeding.  
- Shows alerts if authorization fails or User ID is missing.

#### Conditional Debug Buttons (Testing Mode Only)
- Button to show scheduled notifications in a sheet (`PendingNotificationView`).  
- Button to clear all pending and delivered notifications and reset related state.

#### Survey Presentation
- Listens for notification triggers to decide when to show surveys.  
- Randomly selects from a set of survey views when showing the survey sheet.

#### Lifecycle Event Handlers
- `onAppear`  
  - Loads the stored User ID from persistent storage.  
  - Sets up notifications if this is the first appearance.  
  - Prompts for User ID if none exists.  
- `onChange(of: scenePhase)`  
  - Resets internal flags and re-schedules notifications when app becomes active.

#### Helper Methods

- **`getRandomSurveyView()`**  
  Returns a random survey view from a predefined array of survey components to vary user experience.

- **`setupNotifications()`**  
  Checks notification permissions and schedules notifications if authorized.

- **`fetchPendingNotifications()`**  
  Retrieves pending local notifications from the system, updates UI state, and triggers scheduling if none are found.

#### PendingNotificationView
- A SwiftUI view showing a list of upcoming scheduled notifications.  
- Supports dismissing via a "Close" button.

---

### Summary

`MainView.swift` is the central UI hub that coordinates user identity, HealthKit data sync, notification scheduling, and survey presentation on the Apple Watch. It balances user interaction with background data tasks and system event handling, providing clear feedback and control over key app functionalities. Debug options allow for testing notification management during development.



[Back to Top](#top)
