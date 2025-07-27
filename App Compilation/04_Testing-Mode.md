## 1.04. Testing Mode 

In the DOSE Watch App, you’ll find a section near the top of the MainView.swift file that looks like this:


```swift
// ============================================================================================= //
// Testing mode flag: 1 = show debug buttons; 0 = hide debug buttons
let isTestingMode = 1
// ============================================================================================= //

```
This `isTestingMode` **flag** allows you to toggle the app between two modes: 
- Testing Mode ON
- Testing Mode OFF


---

### 1.04.1. Testing Mode ON (`isTestingMode = 1`)

This mode is intended for **developers or researchers during setup or pilot testing**. When testing mode is ON, the app shows **extra debug buttons** on the watch screen, including:

- “Show Scheduled” – Opens a list of all upcoming scheduled survey notifications.
- “Clear Notifications” – Instantly removes all scheduled and delivered notifications from the watch and clears internal state tracking.

These debug tools help ensure that:
- Notifications are scheduled correctly.
- Survey delivery timing works as expected.
- The internal notification logic (especially re-scheduling) is functioning.



---

### 1.04.2. Testing Mode OFF (`isTestingMode = 0`)

This is the **default setting for real-world deployment** with participants. When the app is in production mode:

- The debug buttons are hidden.
- Users only see the participant-facing components (e.g., their User ID, Upload button, survey prompts).

This prevents any accidental interference with notification scheduling or app logic by study participants.

---

### 1.04.3. How to Change the Mode

To change the testing mode:

1. Open `MainView.swift` in Xcode.

2. Find the following line near the top:

```swift
let isTestingMode = 1
```
By default, the project is set for a testing mode.

3. Change the value to `0` if you’re preparing the app for a participant:

```swift
let isTestingMode = 0
```

4. Rebuild the app and deploy it to the Apple Watch.

---

### 1.04.4. When Should You Use Testing Mode?

Use **testing mode**:
- During app setup or testing before a study launch.
- If you want to verify how notifications are scheduled on a real device.
- While debugging issues like missed surveys or timing mismatches.

Turn **testing mode OFF** when:
- You're distributing the app to participants.
- You want to ensure they cannot modify, clear, or inspect the internal schedule logic.




[Back To Top](#top)
