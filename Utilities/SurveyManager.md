## Overview of `SurveyViewModel.swift`

This file defines the `SurveyManager` class and the `SurveyDataPayload` struct, which together handle the creation, formatting, and saving of survey data from the Dose Watch App. It manages survey state, formats date/time, and uploads survey results securely.

---

### Key Elements

#### `SurveyDataPayload` Struct  
- Represents the data model for survey submissions.  
- Conforms to `Codable` for easy serialization.  
- Properties include:  
  - `surveyNo`: Identifier for the survey.  
  - `minutes`: Duration or related time metric.  
  - `currentDate`: Submission timestamp formatted as a string.  
  - `delivered` and `opened`: Strings tracking notification delivery and open status.  
- Provides a helper method `asDictionary()` to convert the payload into a dictionary format suitable for Firebase or other JSON-based APIs.  
- Contains a date formatting method `formatAsISO8601(date:)` to format dates in local time zone for consistent timestamping.

#### `SurveyManager` Class  
- Implements the **singleton pattern** for shared access across the app:  
```swift
static let shared = SurveyManager()
```

- **Published properties to control survey UI display:**  
  - `showSurveySheet` — Controls whether the survey sheet is visible.  
  - `showSurveyExpiredView` — Controls visibility of an expired survey notification.

- **Tracks survey-related variables:**  
  - `surveyNo`  
  - `minutes`  
  - `notificationPayload` — Holds delivery and open status of notifications.

- **`saveSurvey()` function:**  
  - Retrieves the watch's unique device ID via `StorageManager`.  
  - Creates a `SurveyDataPayload` instance with current survey data and timestamps.  
  - Calls `FirebaseManager.saveSurveyDataToFirebase` to upload the data.

- **Date formatting:**  
  - Contains a duplicate `formatAsISO8601(date:)` method (similar to the one in `SurveyDataPayload`) for consistent date formatting within the manager.

---

### Summary

`SurveyViewModel.swift` provides a clean abstraction for survey data handling in the Dose Watch App. It models the survey data, formats timestamps appropriately, controls survey UI visibility, and securely uploads survey results with device identifiers to Firebase, enabling reliable data collection from users.




[Back to Top](#top)



