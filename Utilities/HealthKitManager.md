## Overview of `HealthKitManager.swift`

The `HealthKitManager` is a singleton class responsible for:

- Requesting HealthKit authorization
- Fetching step count and heart rate data
- Uploading this data to Firebase
- Tracking upload status for user feedback

**Sample Flow:**
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

### Anchors, UI Feedback, Singleton, and Sample Flow

#### Anchors & Timestamps  
To efficiently fetch **only new data incrementally**, the app uses anchors stored in `StorageManager` under keys like `"HeartRateAnchor"` and `"StepCountAnchor"`. These anchors represent the timestamp of the last successfully uploaded data sample. After each successful upload, the anchor is updated with a small buffer (typically 20 seconds) to reduce duplicate or overlapping records when querying HealthKit.

#### UI Feedback  
The app provides live status updates through the `@Published var healthKitStatus` property in `HealthKitManager`. This observable can be bound directly to SwiftUI UI elements (such as text labels) to inform the user about the current syncing state—e.g., authorizing, fetching data, uploading, or completed.

#### Singleton Pattern  
The `HealthKitManager` is implemented as a singleton to ensure a single, consistent instance manages HealthKit access and synchronization across the app. This is important to coordinate syncing actions triggered from multiple views or components.

```swift
static let shared = HealthKitManager()
```

---

### How to Get Other Types of HealthKit Data

#### 1. Request Authorization for New Data Types
HealthKit requires explicit user permission for each type of health data your app wants to read or write. Currently, your `requestAuthorization` function requests authorization only for `.stepCount` and `.heartRate`. To get other types, you would add those `HKObjectType`s to the `allTypes` set, for example:

```swift
let newTypes: Set = [
    HKObjectType.quantityType(forIdentifier: .sleepAnalysis)!,
    HKObjectType.quantityType(forIdentifier: .bodyMass)!,
    HKObjectType.quantityType(forIdentifier: .activeEnergyBurned)!
]
let allTypes = newTypes.union([
    HKObjectType.quantityType(forIdentifier: .stepCount)!,
    HKObjectType.quantityType(forIdentifier: .heartRate)!
])
```

#### 2. Fetch Data for the New Types  
Reuse or extend the existing `fetchHealthKitData(dataType:completion:)` method to query the new data types. For quantity types, this works as is. For other sample types like sleep analysis (which uses `HKCategorySample`), implement similar queries tailored to those sample classes.

#### 3. Handle Different Data Formats  
Since HealthKit data can be of different sample classes (`HKQuantitySample`, `HKCategorySample`, etc.), adapt your data processing and upload logic to handle each type appropriately. This may include parsing different properties or metadata.

#### 4. Manage Anchors and Syncing  
Maintain anchor timestamps or other markers for each new data type to efficiently fetch only new or updated samples. This prevents redundant processing and conserves device resources.

#### 5. Update UI and Status Reporting  
Consider expanding your status enum (`HealthKitStatus`) and UI feedback mechanisms to inform users about the progress and success of syncing new data types.








[Back to Top](#overview-of-healthkitmanagerswift)








