## Overview of `NotificationManager.swift`

The `NotificationManager.swift` is the core part of the app responsible for managing all local notifications. It handles everything from requesting user permission to show notifications, deciding when and how often to deliver them, to managing and updating scheduled notifications efficiently.

This notification system is especially designed for research or behavioral studies where participants need timely reminders—such as prompts to complete surveys or tasks—spread across multiple days and specific time windows. It ensures reminders arrive reliably without overwhelming the device or exceeding Apple’s limits on how many notifications can be pending at any time.

Since this is a standalone watch app, all notifications are scheduled and managed locally on the watch using Apple’s `UNUserNotificationCenter` framework. This means the watch doesn’t depend on a connected iPhone or internet connection to deliver reminders, making the notifications fast, dependable, and independent.

The manager carefully plans notification times across configurable daily windows and checks regularly to schedule new notifications before the current set runs out. This design prevents duplicate notifications or excessive scheduling, maintaining a smooth user experience.

By centralizing all notification logic in one place, this module makes it straightforward to adjust scheduling parameters, test notification behavior, and maintain the app over time—crucial for studies that rely on consistent, timely participant engagement.


### Key Functionalities

- **Permission Management**: It checks the current notification permission status and gracefully handles the cases where permission is denied or not yet determined. It can present user-facing alerts if permission is missing.

- **Notification Scheduling**: The system schedules future notifications based on the app's internal configuration. It supports scheduling multiple notifications per day, spaced across different time windows, and ensures they are distributed randomly within those windows for natural user engagement.

- **State Persistence**: It stores the last date notifications were scheduled using persistent storage (`UserDefaults`). This allows the app to make smart decisions on whether and when to schedule more notifications without duplicating existing ones.

- **Notification Cleanup**: Before scheduling new notifications, the system removes all previously scheduled notifications. This ensures the queue remains clean and avoids conflicts, duplicate delivery, or exceeding system limits.

- **Testing and Debugging Support**: The code includes methods to print all currently pending notifications and to manually schedule example notifications. This aids in QA, debugging, and verifying correct behavior during development.

---

### Customizable Parameters for Notification Scheduling

* The notification scheduling system uses five key parameters to control when and how notifications are created and delivered. These parameters can be adjusted to fit the needs of different studies or user requirements.

* Researchers or developers can easily adjust the notification behavior by modifying five key parameters located near the top of the `NotificationManager.swift` file. These constants are clearly marked for easy identification and customization. By updating these values, researchers can tailor the timing, frequency, and duration of notifications to fit the specific needs of their study or app usage scenario — all without changing the underlying scheduling logic.

```swift
// ============================================================================================= //
// Constant parameters for notification scheduling - update as needed
let maxDaysToSchedule = 10 // notifications scheduled for the next 10 days
let daysBeforeSchedulingMore = 3 // if current time falls within 3 days before the end of the scheduled window, add notifications for the next 10 days
let firstWindowStartHour = 8 // 8:00 AM
let windowLength = 4 // 4-hour length each window
let numberOfWindows = 3 // 3 windows per day
// ============================================================================================= //
```

#### 1. `maxDaysToSchedule`
- **What it does:** Determines how many days ahead the app schedules notifications in advance.  
- **Example:** If set to 10, the app will keep notifications scheduled for about the next 10 days.  
- **Why it matters:** Scheduling too far ahead can clutter the device’s notification queue and exceed platform limits.

#### 2. `daysBeforeSchedulingMore`
- **What it does:** Sets how many days before the end of the current scheduled notifications the app should schedule additional notifications.  
- **Example:** If set to 3, the app will schedule more notifications when there are 3 or fewer days left in the schedule.  
- **Why it matters:** Helps maintain a continuous stream of notifications without gaps, even if the user doesn't open the app every day.

#### 3. `firstWindowStartHour`
- **What it does:** The hour of the day when the first notification window begins.  
- **Example:** If set to 8, notifications will start appearing no earlier than 8:00 AM each day.  
- **Why it matters:** Ensures notifications arrive at appropriate and expected times during the day.

#### 4. `windowLength`
- **What it does:** Defines how long each notification window lasts (in hours).  
- **Example:** If set to 4, each window covers a 4-hour time block (e.g., 8 AM to 12 PM).  
- **Why it matters:** Controls the spread of notifications within the day, helping avoid clustering all notifications at once.

#### 5. `numberOfWindows`
- **What it does:** Sets how many notification windows there are per day.  
- **Example:** If set to 3, the day is divided into 3 separate time windows when notifications can be scheduled.  
- **Why it matters:** Allows notifications to be distributed across the day rather than all at one time.


* These five parameters collaboratively shape the notification scheduling strategy. The app divides each day into a number of windows (`numberOfWindows`), each lasting a specified length (`windowLength`), starting from a set hour (`firstWindowStartHour`). Within each window, notifications are randomly scheduled to avoid predictability. The app keeps a rolling schedule of notifications extending several days into the future (`maxDaysToSchedule`), ensuring users receive timely reminders without overwhelming the system. To maintain continuity, it monitors how close the scheduled notifications are to running out and triggers scheduling more notifications when the remaining window falls below a threshold (`daysBeforeSchedulingMore`). This dynamic approach balances notification frequency, timing, and system constraints to provide a reliable and user-friendly experience.

---

### How `maxDaysToSchedule` and `daysBeforeSchedulingMore` Work Together and Why We Need Both?

**To ensure notifications are scheduled efficiently and continuously, the app relies on two complementary parameters:**
* `maxDaysToSchedule` controls how far ahead notifications are scheduled, and
* `daysBeforeSchedulingMore` controls when to schedule additional notifications.

**Using only one of these variables causes problems:**
* Only `maxDaysToSchedule`: You’d need to re-check and reschedule every day, risking duplicate notifications piling up.
* Only `daysBeforeSchedulingMore`: You wouldn’t know how far into the future to schedule notifications — the app might only add a small number each time it checks, resulting in incomplete scheduling.

**Using both together ensures:**  
- A fixed size of scheduled notifications defined by `maxDaysToSchedule` — this determines how far ahead notifications are planned, and  
- A smart “refill point” set by `daysBeforeSchedulingMore` — this triggers scheduling of new notifications before the existing ones run out.

To help understand, imagine managing a pantry:

| Parameter                  | Pantry Analogy                                               |
|---------------------------|--------------------------------------------------------------|
| `maxDaysToSchedule`       | The total amount of food you want stocked (e.g., 10 days)    |
| `daysBeforeSchedulingMore`| When you decide to go shopping (e.g., when fewer than 3 days left)     |

You don’t want to shop every day (inefficient), but you also don’t want to run out of food (missed notifications).

**What Happens If the App Isn’t Opened?**
- Without `daysBeforeSchedulingMore`, if a user doesn't open the app on the final day of the scheduled window (the end of `maxDaysToSchedule`), no new notifications will be scheduled. This causes notifications to stop, potentially interrupting the study.
- With `daysBeforeSchedulingMore`, the app has a buffer period — as long as the user opens the app within that buffer, it will schedule the next chunk of notifications, greatly improving reliability.

---

### Respecting iOS’s Limit on Pending Notifications
iOS imposes a hard limit of 64 pending notifications per app. To adhere to this, the app ensures the total number of scheduled notifications (calculated as `numberOfWindows` × `maxDaysToSchedule`) does not exceed 64. Exceeding this limit could result in unexpected dropped notifications, which would harm study integrity. Researchers must balance notification frequency and scheduling horizon accordingly.

* You must ensure the product of `numberOfWindows` * `maxDaysToSchedule` ≤ 64

Example:
```swift
let numberOfWindows = 3
let maxDaysToSchedule = 10
```
→ 3 × 10 = 30 notifications → OK

```swift
let numberOfWindows = 4
let maxDaysToSchedule = 20
```
→ 4 × 20 = 80 notifications → Too many — iOS will keep only the latest 64 and silently drop the rest.

---

### Randomized Notification Timing Within Each Window

To avoid sending notifications at the exact same times every day (to helping avoid predictability), the app picks a **random time** to schedule the notification within each defined time window. For each notification window:
- A random **hour** is selected between the window’s start and end hour.
- A random **minute** is selected between 0 and 59.

```swift
for windowIndex in 0..<numberOfWindows {
    let windowStartHour = firstWindowStartHour + (windowIndex * windowLength)
    let windowEndHour = windowStartHour + windowLength
    let randomMinuteOffset = Int.random(in: 0..<60)   // Random minute within the hour
    let randomHour = Int.random(in: windowStartHour..<windowEndHour)  // Random hour within window
    guard let triggerDate = calendar.date(bySettingHour: randomHour, minute: randomMinuteOffset, second: 0, of: targetDate) else { continue }

    scheduledDates.append(triggerDate)
}
```

For example, if a window runs from 8:00 AM to 12:00 PM, the notification might be scheduled at 9:17 AM on one day and 11:45 AM another day. This randomization helps reduce predictability and encourages more natural, timely user engagement.

---

### Additional System Features and Their Importance

* **Cleanup of Old or Pending Notifications**: Before scheduling new notifications, the system removes any previously scheduled notifications that have not yet been delivered. This prevents notification duplicates and ensures that only the most relevant, up-to-date notifications remain in the queue. This cleanup is critical for maintaining an organized notification schedule and avoiding user confusion.
* **Storage of the Last Scheduled Notification Date:** The app saves the date of the most recently scheduled notification using `UserDefaults`. This stored date enables the app to intelligently decide when to schedule more notifications. For example, it will only schedule additional notifications if the last scheduled notification is approaching within a certain window (`daysBeforeSchedulingMore`). This smart scheduling avoids excessive or redundant notification creation, conserves device resources, and keeps notifications timely. 
* **Debugging and Testing Tools:** The code includes helpful utilities such as printing all currently pending notification times and the ability to schedule a single notification for testing. These tools allow developers and researchers to verify notification schedules, troubleshoot timing issues, and validate changes before deploying or releasing the app.

---

**For researchers,** this means that as long as participants open the app within the defined time window (daysBeforeSchedulingMore), the system will seamlessly maintain a randomized, continuous schedule of reminders throughout the study duration. This helps maximize adherence and data completeness without manual intervention.

**For developers,** these mechanisms provide robustness, configurability, and transparency in the notification scheduling process, enabling easier maintenance and adaptation for different study protocols or user requirements.


[Back to Top](#overview-of-notificationmanagerswift)

