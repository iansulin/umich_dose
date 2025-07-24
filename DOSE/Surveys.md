
## 1.03. ESM/EMA Surveys and Response Logic

This chapter describes how the DOSE Apple Watch app supports experience sampling method (ESM) and ecological momentary assessment (EMA) workflows. It focuses on how survey prompts are scheduled, delivered, expired, and logged—all within the constraints of watchOS and without requiring a continuous network connection.

---

### 1.03.1. Randomized Prompt Scheduling

DOSE uses a custom **local scheduling mechanism** to deliver survey prompts at **randomized times** throughout the day. Randomization is a core principle of ESM/EMA design, enabling researchers to capture behaviors and experiences in the moment while minimizing anticipation bias and habitual responses. By avoiding predictable or repeated schedules, this approach reduces systematic confounds and enhances the ecological validity of study findings.

Researchers configure this behavior by specifying customizable parameters such as:

* The number of time windows per day (e.g., 3 or 5)
* The survey start time (earliest prompt delivery time; e.g., 9:00 AM, 1:00 PM, 6:00 PM)
* The length of each time window in hours (e.g., 3)

The app then schedules one randomized prompt within each window. 

**Example 1.** Survey start time = 9 AM; Number of windows per day = 3; Window length (hours) = 3

| Window start time | Selected schedule (random prompt time) |
|-------------------------|---------------------------------------|
| 9:00 AM                 | 10:17 AM                              |
| 1:00 PM                 | 3:05 PM                               |
| 6:00 PM                 | 7:44 PM                               |

**Example 2.** Survey start time = 8 AM; Number of windows per day = 5; Window length (hours) = 2

| Window start time | Selected schedule (random prompt time) |
|-------------------------|---------------------------------------|
| 8:00 AM                 | 8:38 AM                               |
| 10:30 AM                | 11:22 AM                              |
| 1:00 PM                 | 2:18 PM                               |
| 4:00 PM                 | 5:05 PM                               |
| 7:00 PM                 | 7:53 PM                               |


**Example 3.** Survey start time = 5 PM; Number of windows per day = 4; Window length (hours) = 1

| Window start times | Selected schedule (random prompt time) |
|-------------------------|---------------------------------------|
| 5:00 PM                 | 5:10 PM                               |
| 6:00 PM                | 6:38 PM                              |
| 7:00 PM                 | 7:41 PM                               |
| 8:00 PM                 | 8:00 PM                               |

---

### 1.03.2. Survey Notification

When a randomized prompt is due:

* The watch displays a local notification and vibrates via haptic feedback.
* If the user taps the notification, the DOSE app opens directly into the relevant in-app survey interface.
* If the user interacts with the notification, the following events are logged:
     * **Delivered time** (when the notification appeared)
     * **Opened time** (when the user tapped the notification)
     * **Survey response**, **submitted time** and **content**, including the **survey number or identifier** (if submitted)

These logs are temporarily stored locally on the watch and uploaded to Firebase when an internet connection becomes available.

---

### 1.03.3. Survey Expiration

Each survey has a built-in expiration condition: if the user does not respond **before the next scheduled survey prompt**, the previous survey is marked as expired. When the user opens an expired prompt, the app displays an “Expired” message, and the event is logged with `"response": "Expired"`.

This logic ensures that only one active survey window exists at a time, and that responses are collected in close proximity to the scheduled prompt. It reinforces the time-sensitivity and momentary nature of ESM/EMA methods, helping researchers maintain data validity without overlap or delayed entries.

---

### 1.03.4. Notification vs. Response Logging

DOSE stores notification logs and survey responses in separate branches in the Firebase Realtime Database. 

This structure allows researchers to identify:
* Notifications that were opened but not completed (e.g., user opened the survey but exited prematurely, or the app was interrupted)
* Surveys that were submitted late or not at all
* Compliance patterns over time

Unfortunately, due to watchOS limitations, notifications that were never opened cannot be detected or logged.





[Back to Top](#top)






