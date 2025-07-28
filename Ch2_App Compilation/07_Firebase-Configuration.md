## 02.7. Firebase Configuration

This app uses Firebase Realtime Database to store notification details, HealthKit data, and survey responses. The connection to your Firebase backend is managed through the `FirebaseManager.swift` file.


--- 

### 02.7.1 Where to Set Your Firebase Database URL
In FirebaseManager.swift, you will find a constant at the top of the file:
```swift
// ============================================================================================= //
// Firebase Realtime Database base URL                                                           //
static var databaseURL: String = "Replace with your Firebase Realtime Database base URL (no trailing slash)"
// ============================================================================================= //
```

* **To customize your Firebase connection:**

1. **Go to your Firebase Console** at https://console.firebase.google.com and open your project.
2. Navigate to the **Realtime Database** section.
3. Copy the database URL shown at the top (it usually looks like `https://your-project-id.firebaseio.com`).
4. Replace the existing `databaseURL` value in `FirebaseManager.swift` with your own Firebase Realtime Database URL, **making sure not to add a trailing slash**.

```swift
static var databaseURL: String = "https://your-project-id.firebaseio.com"
```

---

### 02.7.2. What Happens When You Change the URL

* The app uses this base URL to build paths when saving data to Firebase.
* All data saved (notifications, HealthKit data, survey results) will be sent to the database under paths like `/devices/{deviceID}/...`
* This lets you keep your study data separate and organized by each device's unique ID.

---

### 02.7.3. Additional Firebase Setup Notes

* This app communicates with Firebase using **direct HTTPS requests** to the Realtime Database REST API.
* Make sure your Firebase Realtime Database rules allow **write access** to the paths your app writes to (e.g., `/devices/{deviceID}/...`).


[Back to Top](#top)




