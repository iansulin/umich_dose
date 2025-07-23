## Overview of `ParticipantView.swift`

`ParticipantView.swift` defines the user interface for entering a unique User ID when the Dose Watch app is first installed or if no ID is found. This ID is essential for identifying data associated with the user and is stored using `StorageManager`. It plays a critical role in the **onboarding process** and ensures consistent user tracking throughout the app’s lifecycle.


---

### Interface Behavior

- **NavigationView + ScrollView**:
  - Provides a vertical, scrollable layout suitable for the Apple Watch screen.

- **User ID TextField**:
  - Users input their identifier in a single-line text field.
  - Autocorrection is disabled to reduce input errors.
  - Text entry is bound to a `@Binding` variable (`userID`), ensuring real-time updates.

- **Instructional Text**:
  - Small, gray-colored guidance below the text field reminds users that the ID is permanent and should be entered carefully.

- **Two Icon Buttons**:
  - **Cancel**: Dismisses the screen without saving changes.
  - **Save**: Saves the user ID after validating that it:
    - Is not empty.
    - Is not a placeholder like “test” or “demo”.

- **Alerts**:
  - Error messages are displayed in a modal alert when invalid input is detected.

---

### User ID

The User ID is a persistent identifier used to link all data uploaded from the Apple Watch to Firebase. Once submitted, it becomes part of the data lineage and backend structure. **The ID is locked after first entry.**

- A `TextField` allows participants to enter a unique User ID.
- Autocorrection is disabled to avoid unintended modifications.
- A gray instructional note reminds users that this ID **cannot be changed later** and should be entered carefully.


#### Why User ID Cannot Be Changed

The User ID is a critical identifier that links all data generated on the device — including HealthKit metrics, survey responses, and notification logs — to a unique participant. Once saved, this ID is used as a **key in Firebase** and other persistent stores.

**Changing the User ID later would:**
- Break linkage between previously uploaded data and the user.
- Risk data integrity and loss of historical context.
- Require complex re-association logic that compromises privacy and security.


#### What to Do If a Typo Is Made

If the User ID is entered incorrectly (e.g., due to a typo), developers or study coordinators have two options:
1. **Manually reset the app**:
   - Uninstall and reinstall the app to trigger the onboarding flow again.
   - Or, clear the User ID programmatically during development or testing using a debug/reset option if one is implemented.
2. **Flag and disregard incorrect data**:
   - If the ID is already used and data has been uploaded, the backend team can ignore or archive that user record and start fresh with a new one.
It is strongly advised to double-check the User ID before submission and avoid generic or placeholder values like `"test"`.

#### Validation Logic

- If the user taps the **save button**:
  - The app checks if the input is empty. If so, an alert is shown.
  - If the input is `"test"` or `"demo"`, the app shows a validation alert requiring a more meaningful ID.
  - If valid, the ID is saved via:
    ```swift
    StorageManager.shared.getUniqueIdentifierForWatch(userSuppliedDeviceID: userID)
    ```
  - The view is then dismissed.

- If the user taps **cancel**, the view is dismissed without saving.

---

### When Is This View Shown?

Typically triggered when:
- The app is first launched and no User ID exists.
- Resetting/testing the app (e.g., in testing mode).
- Required by a flow that depends on binding the participant to data.

Once a valid ID is saved, the user typically does **not** see this screen again unless reset.



[Back to Top](#top)
