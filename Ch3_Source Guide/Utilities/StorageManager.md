## Overview of `StorageManager.swift`

This file defines the `StorageManager` class, a singleton responsible for managing persistent storage related to the app. It primarily interacts with **Keychain** for secure data storage and **UserDefaults** for simple preferences.

---

### Key Elements and Their Descriptions

#### Singleton Pattern  
```swift
static let shared = StorageManager()
```
* Ensures a single shared instance of `StorageManager` is used throughout the app for consistent access to stored data.

#### Keychain Storage Keys
- **keychainWatchIDKey** — Stores a unique identifier for the watch device.  
- **keychainFirstLaunchKey** — Stores the app’s first launch date on the watch.  
- **keychainService** — The service identifier for the Keychain instance: `"com.doself.notifications.app"`.

#### Device Identifier Management
- **getUniqueIdentifierForWatch(userSuppliedDeviceID:)**: Retrieves or sets a unique device identifier in Keychain.  
- If running in the simulator, prefixes the identifier with `"TEST-"` for testing purposes.  
- **removeUniqueIdentifierForWatch()**: Removes the stored watch identifier from Keychain.

#### First Launch Date & Day Counting
- **fetchLaunchDate()**: Retrieves the app’s first launch date from Keychain (stored as an ISO8601 string).  
- **calculateDaysSinceLaunch()**: Returns the number of days since the first launch, counting the first day as 1.  
  - If the launch date doesn’t exist yet, it sets the current date (midnight) as the launch date and returns 1.

#### Anchor Key Storage for Incremental Syncing
- Anchors are timestamps stored in Keychain to track the last synced HealthKit data point for each data type.  
- **saveAnchorKey(_:forKey:)**: Saves a double (timestamp) as a string under a given key.  
- **loadAnchorKey(forKey:)**: Loads a stored timestamp from Keychain, returning it as a `Double?`.

#### UserDefaults for Notification Scheduling
Keys for tracking notification-related info:  
- **lastScheduledNotificationDateKey** — Stores the last date notifications were scheduled.  
- **latestNotificationTimeKey** — Stores the most recent notification trigger time as a `TimeInterval`.  

Functions to save, retrieve, and clear these dates and times from UserDefaults:  
- `saveLastScheduledNotificationDate(date:)`, `getLastScheduledNotificationDate()`, `clearLastScheduledNotificationDate()`  
- `saveLatestNotificationTime(triggerTimeInterval:)`, `getSavedLatestNotificationTimeInterval()`

---

### Summary
`StorageManager` provides secure and persistent management of critical app data such as device identifiers, launch dates, HealthKit syncing anchors, and notification scheduling times. It combines **Keychain** for sensitive, secure storage and **UserDefaults** for lightweight preference storage, ensuring data consistency and integrity across app launches and sessions.


[Back To Top](#top)
