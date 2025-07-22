## Overview of `FirebaseManager.swift`

The `FirebaseManager.swift` provides a centralized way to upload user data from the watch app to Firebase Realtime Database. It supports uploading:

- Notification delivery logs
- HealthKit-derived data arrays
- Survey responses

All functions use `URLSession` to communicate with Firebase via POST requests and include basic error handling. This manager is designed for standalone watchOS apps, where the device directly sends data to Firebase without relying on a paired iPhone.

---

### Configuration

At the top of the file, update the Firebase Realtime Database URL:

```swift
// ============================================================================================= //
// Replace with your Firebase Realtime Database base URL (no trailing slash)                     //
static var databaseURL: String = "https://your-project-id.firebaseio.com"
// ============================================================================================= //
```

* The `databaseURL` is the base web address of your **Firebase Realtime Database**. Your app uses this URL to know **where to send data** (e.g., survey responses, notification logs, HealthKit data). If this value is missing or incorrect, your app won’t be able to upload any data to Firebase.

---

### Where to Find the `databaseURL`

**Prerequisite**: You need to have a Firebase project already set up. If not, go to https://console.firebase.google.com and create one.

1. **Go to the Firebase Console**
* Visit https://console.firebase.google.com
* Sign in and select your Firebase project.

2. **Open the Realtime Database Section**
* From the left sidebar, click on **"Build" → "Realtime Database"**.
* If you haven’t already created one, click **“Create Database”** and choose a location (usually `us-central1` is fine).

3. **Copy Your Database URL**
* At the top of the Realtime Database page, you’ll see your database URL.
* It will look like:
```arduino
https://your-project-id.firebaseio.com
```
* This is the value you should copy and paste into your `FirebaseManager.swift`:
```swift
static var databaseURL: String = "https://your-project-id.firebaseio.com"
```

**MAKE SURE:** Do not include a trailing slash at the end of the URL.
* Correct: `"https://your-project-id.firebaseio.com"`
* Incorrect: `"https://your-project-id.firebaseio.com/"`

---

### `saveNotification(...)` uploads notification delivery logs

* **Purpose:** To record when a notification was sent and what it contained (e.g., time, type, delivery window).

* **How it works**
  - Takes a `NotificationPayload` struct  
  - Converts it to a dictionary  
  - Uploads it to Firebase under:  
```bash
/devices/<deviceID>/notifications/
```

* Example Firebase entry:
```json
{
  "timestamp": "2025-07-22T08:00:00Z",
  "type": "reminder",
  "windowIndex": 1
}
```
This allows researchers to track when prompts were delivered and match them with user responses or behaviors.

---

### `saveHealthKitDataArrayToFirebase(...)` uploads HealthKit-derived data arrays

* **Purpose:** To upload batches of HealthKit-derived data (e.g., steps, heart rate, sleep) as structured dictionaries.

* **How it works:**
  - Accepts an array of `[String: Any]` dictionaries
  - Uploads all data points to:
```bash
/devices/<deviceID>/healthkitdata/
```

* **Benefits:**
  - Efficiently captures passive sensing data
  - Enables rich analysis of physiological or behavioral trends

---

### `saveSurveyDataToFirebase(...)` uploads user-entered survey responses

* **Purpose:** To store user responses to surveys or EMA-style questions (e.g., mood, stress level, habits).

* **How it works:** 
  - Accepts a SurveyDataPayload struct
  - Converts it to a dictionary
  - Uploads it to Firebase under:
```bash
/devices/<deviceID>/survey/
```

* Example Firebase entry:
```json
{
  "questionID": "mood_today",
  "response": "Happy",
  "timestamp": "2025-07-22T09:00:00Z"
}
```
This allows researchers to correlate self-reported data with notifications or sensor data over time.










[Back to Top](#top)







