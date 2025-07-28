## Overview of `SurveyExpiredView.swift`

`SurveyExpiredView.swift` is a survey expiration notification interface that defines a simple SwiftUI view displayed when a previously available survey has expired due to the publication of a newer survey. It notifies the user and records the expiration event in the app's backend.

---

### Interface Components

- **Title Text**:  
  Displays "Survey Expired" in a large font at the top.

- **Description Text**:  
  Explains that the current survey is no longer valid because a newer survey was published.  
  - Uses a secondary text color to indicate less emphasis.  
  - Supports multiple lines with flexible sizing.

- **Dismiss Button**:  
  A tappable button labeled "Dismiss" at the bottom that closes the view by setting `showSurveyExpiredView` to false on the shared `SurveyManager` instance.

- **Layout**:  
  Vertical stack (`VStack`) with spacing and padding to separate the title, message, and button.  
  Uses flexible spacers to center content vertically and fills the available space.

---

### Functional Behavior

- When the view appears (`.onAppear`), it saves a record indicating the survey expiration via `SurveyManager.saveSurvey()`.  
- The `recordSaved` boolean state prevents multiple saves if the view reappears.  
- This save marks the survey number as `"Expired"` and sets minutes to `"0"`, signifying no user interaction.

---

### Design & User Experience

- Minimal and clear interface focusing on communicating the expiration status.  
- Clean typography and spacing adapted for Apple Watch's small screen.  
- The dismiss button provides a straightforward way to exit the message.  
- No navigation bar is shown to maintain a simple modal appearance.

---

### Summary

`SurveyExpiredView.swift` provides an essential UX component for handling survey lifecycle in the App. It informs users when surveys are no longer active and ensures backend logging of such events for tracking purposes.


[Back to Top](#top)
