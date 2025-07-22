## Overview of `DoseApp.swift`

This code is the app’s **launchpad and notification manager**. It configures and maintains essential services like notifications and shared app state, processes user notification interactions, and integrates with Firebase and HealthKit managers to ensure smooth, reliable survey delivery and response tracking. The UI views reactively respond to changes in these managers, but this struct itself does not contain direct UI — it’s the engine driving app behavior behind the scenes.

---

### Key Responsibilities

#### 1. Main Application Entry Point  
- The `@main` attribute designates this struct as the app’s entry point. When the app launches, SwiftUI runs the code in this struct first.
- It conforms to the `App` protocol, which defines the app’s lifecycle and UI scene(s).

#### 2. Shared State Management  
- The app creates three shared singleton instances as `@StateObject`:
  - `notificationManager` — Handles all notification scheduling, permissions, and Firebase interactions related to notifications.
  - `healthKitManager` — Manages HealthKit permissions, data fetching, and syncing.  
  - `surveyManager` — Manages survey state, including showing/hiding survey UIs and saving survey results.  
- These shared instances are passed into the environment so that any SwiftUI view in the app can observe and interact with this shared state reactively.

#### 3. Initialization (`init`)

- **Notification Delegate Setup:**  
  - Assigns a custom delegate (`NotificationDelegate`) as the handler for notification events from the system. This means when a notification is delivered or tapped, this delegate receives callbacks to process those events.

- **Notification Authorization Request:**  
  - Calls a method to request user permission for sending notifications. This is essential for the app’s survey-triggering notifications to function.

- **Development Testing Code (Commented Out):**  
  - The code to clear all pending and delivered notifications is commented but available for testing purposes to reset notifications during development.

#### 4. App Scene (`body`)
- Defines a `WindowGroup` scene which is the root container for the app UI.
- Displays the `MainView` as the main interface, injecting the shared environment objects (`notificationManager`, `healthKitManager`, and `surveyManager`).
- This setup ensures that `MainView` and its child views can access and respond to changes in app-wide state.

#### 5. Notification Delegate Inner Class

- Implements `UNUserNotificationCenterDelegate` methods to respond when the user interacts with notifications.

- **Handling Notification Responses (`didReceive response`):**  
  - Extracts info about the notification that was tapped, including a timestamp representing when the notification was scheduled (`triggerDate`).  
  - Retrieves all currently delivered notifications to verify that the tapped notification is the most recent one by delivery time.  
  - Checks if this notification's trigger timestamp is later than any previously recorded notification. This ensures the app reacts only to the latest notifications to avoid duplicates or outdated surveys.  
  - If it’s the latest, the app:  
    - Saves the notification timestamp.  
    - Saves notification details (delivery/opened times) to Firebase.  
    - Posts a local notification (`NotificationToLaunchSurvey`) which triggers the UI to display the appropriate survey.  
  - If it’s outdated, it:  
    - Still saves notification details.  
    - Shows a “Survey Expired” UI by updating `SurveyManager`'s state.

- **Saving Notification Details:**  
  - Extracts timestamps from the notification.  
  - Creates a notification payload for Firebase logging.  
  - Updates the survey manager with this info so the UI knows the notification’s status.  
  - Saves details to Firebase.

- **Logic to Check Latest Notification by Time:**  
  - Compares the current notification's trigger time with the saved latest trigger time from persistent storage.  
  - Ensures the app only responds to notifications that are newer than previously handled ones.

---

### How This Fits into the App

- This file acts as the central coordinator for the app’s lifecycle and notification flow.

- It guarantees:  
  - Notification permissions are requested early.  
  - Notifications are properly handled and routed to the right UI components.  
  - Only the newest notification triggers a survey, preventing outdated or duplicate survey prompts.

- The environment objects enable seamless communication between this core app logic and the user interface.





[Back to Top](#top)
