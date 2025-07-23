


## 1.02. Firebase Setup

**Before you begin:**

* Create a Gmail account, if you haven't already, as it's required to access Firebase services.

The DOSE app saves data locally on the watch first—to ensure robustness when offline—and then syncs that data to Firebase Realtime Database via direct REST API calls. Because DOSE uses this direct connection, users do not need to build or host their own server. There’s no need to write backend code or manage server infrastructure—Firebase handles data storage out of the box. Once you enter your Firebase Realtime Database URL into the DOSE app’s configuration, the app will automatically begin sending data to that location. This lightweight approach allows the app to function without integrating Firebase SDKs or adding `plist` files, making deployment simple and fast with only minimal code edits.

However, there are still **a few things you must do before getting started**. You will need to create your own Firebase project (a one-time setup), enable the Realtime Database from the Firebase console, and copy your database URL into the appropriate place in the code. This chapter walks you through each of those steps.

---

### 1.02.1. Firebase Setup

1. **Create a Firebase Project:**
      * Go to [Firebase Console](https://console.firebase.google.com) and create a new project.

2. **Open Your Project**
      * From the dashboard, select the project you just created.

3. **Navigate to Realtime Database**

      * In the left sidebar, click **"Build" > "Realtime Database"**.

4. **Create a Database**

      * Click **“Create Database”** in the Realtime Database section.
      * Choose a location (e.g., us-central1) — this cannot be changed later.
      * Choose the **rules mode**:
         * For testing, you can choose “Start in test mode” (this allows open read/write access temporarily).
         * For production, you’ll want to customize your rules later.

> **Important Note:** The DOSE team does not provide or share specific security rules, as these must be developed based on study-specific or institutional policies. This approach ensures that security protocols align with each study’s unique requirements, considering factors such as data sensitivity, regulatory compliance, and ethical guidelines. For a detailed overview of security rules, their structure, and implementation principles, refer to the [Firebase Security Rules Primer](https://firebase.google.com/docs/rules).

5. **Note your Realtime Database URL**
    * Once created, you will see the database URL in the format: `https://your-project-id.firebaseio.com/`
      * **Store (or remember) this URL for later use. You’ll insert this directly in the app’s configuration code.**




[Back to Top](#top)










