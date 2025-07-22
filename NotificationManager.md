# Notification Scheduling Parameters Explained

### 1. `maxDaysToSchedule`

**What it is:**  
The maximum number of days ahead for which your app will schedule notifications in advance.

**Where it's used:**  
In the method `scheduleNotifications(for days: Int)`, you pass this value as `days`. It means: "Schedule notifications for this many days starting from today (or the last scheduled day)."

**Why it’s useful:**  
You don’t want to schedule too far in advance because:
- It could clutter the notification queue.
- You may want to adjust scheduling based on user behavior or new data.
- iOS limits the number of pending local notifications (typically 64 per app).

**Example:**  
If `maxDaysToSchedule = 10`, the app tries to keep about 10 days' worth of notifications scheduled at any time.

---

### 2. `daysBeforeSchedulingMore`

**What it is:**  
The number of days before the end of your currently scheduled notification window that will trigger the app to schedule more notifications.

**Where it's used:**  
In `checkAndScheduleNotificationsIfNeeded()`, it compares the last date notifications were scheduled for against today’s date plus `daysBeforeSchedulingMore`.

**How it works:**
- If the last scheduled date is more than `daysBeforeSchedulingMore` days in the future, it assumes enough notifications are already scheduled.
- If the last scheduled date is less than or equal to that threshold, it schedules additional notifications to extend the window.

**Why it’s useful:**  
To avoid calling scheduling too often, but also never run out of scheduled notifications. This keeps a rolling "buffer" of notifications ready.

**Example:**  
If `daysBeforeSchedulingMore = 3` and today is July 1, and the last scheduled notification is for July 5, then:  
Since July 5 is less than July 1 + 3 days → the app will schedule more.

---

### How They Work Together

You need both variables to balance:
- Efficiency (don’t schedule too often)
- Coverage (always have notifications queued)

#### Why Not Just One Variable?

- **Only `maxDaysToSchedule`:**  
  You’d need to re-check and reschedule every day, potentially adding duplicate notifications.

- **Only `daysBeforeSchedulingMore`:**  
  You wouldn’t know how far in the future to schedule — the app might only top off with a single day's notification.

Using both ensures:
- A fixed "pantry size" of notifications (`maxDaysToSchedule`)
- A smart "refill point" (`daysBeforeSchedulingMore`) so the pantry never runs out

---

### Analogy

Imagine you're managing a pantry:

| Parameter                  | Pantry Analogy                                               |
|---------------------------|--------------------------------------------------------------|
| `maxDaysToSchedule`       | The total amount of food you want stocked (e.g., 10 days)    |
| `daysBeforeSchedulingMore`| When you decide to go shopping (e.g., when <3 days left)     |

You don’t want to shop every day (inefficient), but you also don’t want to run out of food (missed notifications).

---

### What Happens If the App Isn’t Opened?

Without `daysBeforeSchedulingMore`, if a user doesn't open the app on the final day of the scheduled window (i.e., at the end of `maxDaysToSchedule`), no new notifications will be scheduled.

This means:
- Notifications will stop.
- The study could unintentionally pause or break.

With `daysBeforeSchedulingMore`, you gain a buffer window — as long as the user opens the app within that buffer period, the app will schedule the next chunk of notifications. This greatly improves reliability.

---

### iOS Notification Limit

**iOS enforces a hard limit of 64 pending local notifications per app.**

This means you must ensure: `numberOfWindows` * `maxDaysToSchedule` ≤ 64

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

Recommendation:
Ensure the product of numberOfWindows × maxDaysToSchedule stays ≤ 64


