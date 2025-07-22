## Overview of `HealthKitManager.swift`

The `HealthKitManager` is a singleton class responsible for:

- Requesting HealthKit authorization
- Fetching step count and heart rate data
- Uploading this data to Firebase
- Tracking upload status for user feedback

It integrates closely with:
- **FirebaseManager.swift** – to handle actual uploads
- **StorageManager.swift** – to persist anchors (last-uploaded timestamps)
- **WatchKit UI** – via `@Published` status updates for real-time feedback

---

### HealthKitStatus Enum

Represents the current state of HealthKit-related operations. Used to drive UI feedback.

| Case              | Purpose                               | UI Message                |
|-------------------|----------------------------------------|----------------------------|
| `.none`           | Default/idle                           | *(no message)*             |
| `.unavailable`    | HealthKit not available                | `HealthKit unavailable`    |
| `.authorizing`    | Requesting authorization               | `authorizing...`           |
| `.authorized`     | Authorized                             | `authorized...`            |
| `.unauthorized`   | Permission denied                      | `HealthKit permission required` |
| `.dataerror`      | Error fetching data                    | `Error! retry`             |
| `.connecting`     | Querying HealthKit                     | `fetching health data...`  |
| `.nosteprecords`  | No new step data                       | `No new step data`         |
| `.noheartrecords` | No new heart data                      | `No new heart data`        |
| `.processing`     | In processing state                    | `Processing...`            |
| `.uploading`      | Uploading data                         | `Uploading data...`        |
| `.uploaded(String)`| Upload complete                       | *(custom message)*         |
| `.saved`          | Data saved                             | `Data Uploaded`            |

---

### Core Methods

#### 1. `requestAuthorization(completion:)`
Requests permission to access step count and heart rate data.
- Uses `HKHealthStore.requestAuthorization(...)`
- Updates the UI status based on user decision or error
- Calls the completion block with success/failure


#### 2. `fetchStepCountData()`
a. Checks HealthKit permissions
b. Queries step data from last upload time (using anchor from `StorageManager`)
c. Calls `uploadHealthKitData(...)`
d. Once steps are uploaded, triggers `fetchHeartRateData(...)` to continue the data flow
  * Even if no step data, it still tries to fetch heart rate data

#### 3. `fetchHeartRateData(isOnlyHeartData:)`
- Similar to `fetchStepCountData()`, but for heart rate data
- `isOnlyHeartData = true` indicates it's being run directly (not after steps)
- If upload succeeds, status is updated to `.saved` and cleared after delay

#### 4. `fetchHealthKitData(dataType:completion:)`
* Fetches data of type `.stepCount` or `.heartRate`:
  - Retrieves the anchor timestamp from `StorageManager`
  - Queries data from anchor to `now` using `HKSampleQuery`
  - Calls completion with results

#### 5. `uploadHealthKitData(dataSamples:dataType:completion:)`
* Handles Firebase upload logic:
  - Formats data samples into dictionaries
  - Uses `FirebaseManager.saveHealthKitDataArrayToFirebase(...)`
  - Updates anchor timestamp after successful upload
  - Displays feedback message using `HealthKitStatus.uploaded(...)`

#### 6. `formatAsISO8601(date:)`
* Formats dates for Firebase storage, using local (device) time.
```swift
"2025-07-22 09:13:00AM"
```

---

### Anchors & Timestamps

* Used to **incrementally fetch** only new data:
  - Stored in `StorageManager` under keys:
    - `"HeartRateAnchor"`
    - `"StepCountAnchor"`
  - Updated after successful upload
  - Adds a small buffer (20 seconds) to reduce duplicate/overlap records

---

### UI Feedback

* Driven by the `@Published var healthKitStatus` observable:
  - Updated throughout the process to reflect current state
  - Can be bound to SwiftUI text labels for live feedback

---

### Singleton Pattern

```swift
static let shared = HealthKitManager()
```

This ensures that one consistent instance manages HealthKit access across your app — especially important when multiple views may trigger health data syncs.

---

### Sample Flow

```text
1. User taps "Sync Data"
2. -> requestAuthorization()
3. -> fetchStepCountData()
     -> uploadHealthKitData("Steps")
     -> fetchHeartRateData()
         -> uploadHealthKitData("HeartRate")
4. Anchors updated
5. Status shows "Uploaded X records - ST/HR"
```

---




















[Back to Top](#top)







