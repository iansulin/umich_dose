## 2.05. Customizing Notifications with `NotificationManager.swift`

The `NotificationManager.swift` file is where you can control **when, how often, and at what times survey notifications are sent** to the Apple Watch. If you're customizing your study schedule, this is the main file to modify. You'll find a clearly marked section near the top of the file that looks like this:

```swift
// ============================================================================================= //
// Constant parameters for notification scheduling - update as needed
let maxDaysToSchedule = 3 // notifications scheduled for the next 10 days
let daysBeforeSchedulingMore = 2 // threshold in days before the end of the scheduled window to add more notifications
let firstWindowStartHour = 8 // 8:00 AM
let windowLength = 1 // 4-hour length each window
let numberOfWindows = 13 // 3 windows per day
// ============================================================================================= //
```

These variables represent the **customizable parameters** that control the notification scheduling logic of the entire app. For researchers and study designers, these are the primary settings to adjust when modifying how often and when surveys are delivered. Each parameter is explained in detail below, along with guidance on how to customize them effectively.

- **`maxDaysToSchedule`**: Number of days into the future for which notifications are pre-scheduled.
- **`daysBeforeSchedulingMore`**: How many days before the end of the current schedule the app will add new notifications.
- **`firstWindowStartHour`**: The hour of the day when the first notification window starts.
- **`windowLength`**: The length, in hours, of each notification window.
- **`numberOfWindows`**: Total number of notification windows per day.

Together, these parameters enable flexible control over the timing, frequency, and daily distribution of survey prompts to study participants.

### Section List

- [2.05.1. How Many Days of Notifications Are Scheduled?](#2051-how-many-days-of-notifications-are-scheduled)
- [2.05.2. When Does the App Reschedule More Notifications?](#2052-when-does-the-app-reschedule-more-notifications)
- [2.05.3. What Hours Can Participants Receive Surveys?](#2053-what-hours-can-participants-receive-surveys)
- [2.05.4. How Are Notification Times Randomized?](#2054-how-are-notification-times-randomized)
- [2.05.5. How Does the App Handle Scheduling Failures or Restarts?](#2055-how-does-the-app-handle-scheduling-failures-or-restarts)
- [2.05.6. Where Are Notification Logs Saved?](#2056-where-are-notification-logs-saved)
- [2.05.7. Test Mode for Scheduling a Single Notification](#2057-test-mode-for-scheduling-a-single-notification)
- [2.05.8. Apple Watch Notification Limit: Stay Below 64](#2058-apple-watch-notification-limit-stay-below-64)
- [Notification Scheduling Parameters Summary](#notification-scheduling-parameters-summary)



---

### 2.05.1. How Many Days of Notifications Are Scheduled?

At the top of the file, you’ll see:

```swift
let maxDaysToSchedule = 3
```

This controls how far into the future the app pre-schedules notifications (in this case, 3 days).
- Change this to `5` or `7` if you'd like surveys scheduled a full week ahead.



---

### 2.05.2. When Does the App Reschedule More Notifications?

```swift
let daysBeforeSchedulingMore = 2
```

This means the app will a**utomatically add more survey notifications** when there are fewer than 2 days left in the current schedule.
- If you want more frequent re-checking (e.g., daily), set this to `1`. If you want it to wait until the schedule is almost empty, use `0`.

---

### 2.05.3. What Hours Can Participants Receive Surveys?

```swift
let firstWindowStartHour = 8 // 8:00 AM
let windowLength = 1         // 1-hour block
let numberOfWindows = 13     // 13 windows = 13 notifications/day
```

These parameters define **daily time windows** when the app may trigger a survey. The example above means: Surveys can begin as early as **8:00 AM**; Each time window is **1 hour** long; There are **13 total windows** (last possible survey starts around 8AM + 13 hours = 9:00 PM).

- To start later, change `firstWindowStartHour` (e.g., to `10` for 10AM).
- To send fewer surveys per day, reduce `numberOfWindows`.
- To make each window longer (e.g., 2-hour blocks), increase `windowLength`.

---

### 2.05.4. How Are Notification Times Randomized?

Each scheduled notification is placed at a **random time** within its assigned window. This is done using:

```swift
let randomMinuteOffset = Int.random(in: 0..<60)
let randomHour = Int.random(in: windowStartHour..<windowEndHour)
```

This ensures that participants don’t receive notifications at the same exact time every day.
> This behavior usually doesn't need to be changed, but you can modify it to fine-tune delivery consistency or randomness.

---

### 2.05.5. How Does the App Handle Scheduling Failures or Restarts?

If the app restarts, or if no schedule exists, it will automatically:

- **Clear previous notifications** (to avoid duplicates),
- **Start fresh**, scheduling from the current date forward.

This logic is inside `checkAndScheduleNotificationsIfNeeded()`.
> No need to change this unless you want advanced control over recovery behavior.

---

### 2.05.6. Where Are Notification Logs Saved?

The app logs each **delivered and opened** notification using:

```swift
saveDetailsToFirebase(notificationPayload: ...)
```

This allows backend researchers to track when participants saw and responded to surveys. The unique Watch ID is used as a key.
> This is useful for compliance checks and backend analysis — no changes needed unless you have a custom Firebase setup.

---

### 2.05.7. Test Mode for Scheduling a Single Notification

At the bottom of the file, you’ll find helper functions like:

```swift
func testScheduleSingleNotification()
```

This is useful during pilot testing. You can specify a **hard-coded test date and time** to trigger a survey manually — ideal for debugging.
> Use this when you want to validate the app's behavior without waiting for a scheduled survey.

---

### 2.05.8. Apple Watch Notification Limit: Stay Below 64

Apple WatchOS limits each app to a **maximum of 64 pending local notifications** at any given time. If you exceed this limit, newer notifications will not be scheduled. To ensure continuous delivery without hitting this cap, the app uses a rolling window approach:

- It only schedules up to `maxDaysToSchedule` × `numberOfWindows` notifications.
- It re-checks every time the number of days remaining in the schedule drops below `daysBeforeSchedulingMore`.
- Old delivered or expired notifications are cleared before scheduling new ones.

**Important Consideration:**
If you choose a high number of notifications per day (e.g. numberOfWindows = 24) and also set maxDaysToSchedule to 3 or more, you might hit the 64 notification cap. For example:

* `24 windows × 3 days = 72` ➜ Too many notifications.

To stay safe, ensure:
* `maxDaysToSchedule` × `numberOfWindows` ≤ 60 (buffer recommended)
* e.g., `5 days × 10 windows = 50` is safe

---

### Notification Scheduling Parameters Summary

| Parameter Name | Description | Example Value | 
|-|-|-|
| `maxDaysToSchedule` | How many days ahead to schedule notifications. | `3` | 
| `daysBeforeSchedulingMore`| How many days before end of current window to trigger more scheduling. | `2` |
| `firstWindowStartHour` | Time of day (in 24h format) when the first notification window begins. | `8` |
| `windowLength` | Duration (in hours) of each notification window. | `1` | 
| `numberOfWindows` | Total number of daily windows for potential notifications. | `13` |

> **Tip for Researchers**: If you want 3 daily notifications (morning, afternoon, evening), use:
> - `firstWindowStartHour = 9`
> - `windowLength = 2`
> - `numberOfWindows = 3`













[Back To Top](#top)
