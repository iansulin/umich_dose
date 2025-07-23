
## Prerequisites to Use the DOSE Apple Watch Framework

This project provides a customizable Apple Watch-based framework for collecting both passive health data and active survey responses, synced to your Firebase Realtime Database via REST API. Here's what you need before you begin:

---

### Hardware Requirements

* **Apple Watch** (Series 4 or later) running **watchOS 9+**
* **Mac** running **macOS 13+**

---

### Software Requirements

* **Xcode** (version 14+)
* **Apple Developer Account**
  * Free tier is sufficient for local builds
  * Paid account required for on-device deployment or TestFlight distribution

---

### Firebase Setup

1. Create a Firebase project at console.firebase.google.com
2. Enable Realtime Database
3. Get your database URL, e.g.:
    ```bash
    https://your-project-id.firebaseio.com/
    ```
4. Set Firebase rules to allow your desired level of access. Example (for dev/testing):
    ```json
    {
    "rules": {
      ".read": true,
      ".write": true
      }
    }
    ```
This allows public access. Use authenticated rules for production.

> The DOSE app only writes to a known Realtime Database URL via **direct REST API calls**, you do not need the ```GoogleService-Info.plist``` file, because your app isn't initializing Firebase via the native APIs.

---

### Other Recommendations 

* A consent process or IRB approval, if used in research settings
* TestFlight set up for distributing the app to study participants


[Back to Top](#top)


