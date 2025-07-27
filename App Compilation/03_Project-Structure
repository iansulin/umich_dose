## 1.03. Overview of Project Structure

When opening the DOSE app Xcode project, you’ll find a clean and modular file organization that supports easy navigation and extensibility. The project includes one main app entry point and three primary folders that group related functionality.

```bash
DOSEWatchApp/
├── DoseApp.swift                 # Main app entry point
│
├── Views/                       # Core participant-facing screens
│   ├── MainView.swift
│   ├── ParticipantView.swift
│   └── SurveyExpiredView.swift
│
├── SurveyModules/              # Modular ESM interfaces
│   ├── NumericKeypad.swift
│   ├── ScribbleDictation.swift
│   ├── StepSlider.swift
│   ├── CircularSlider.swift
│   └── ScrollableList.swift
│
└── Utilities/                  # Backend, health, and data managers
    ├── SurveyManager.swift
    ├── StorageManager.swift
    ├── NotificationManager.swift
    ├── HealthKitManager.swift
    └── FirebaseManager.swift
```

---

### 1.03.1. Main Entry Point

- **`DoseApp.swift`**: This is the main application file. It initializes the app’s lifecycle, sets up the environment, and launches the initial interface (`MainView.swift`).

---

### 1.03.2. Views

This folder contains the core user interface screens presented to participants:
- **`MainView.swift`** – The primary view users see when opening the app. It displays the current state and provides access to survey participation.
- **`ParticipantView.swift`** – Presented when a participant is actively engaged with a survey or task.
- **`SurveyExpiredView.swift`** – Displayed when a scheduled survey is no longer available due to expiration.

---

### 1.03.3. SurveyModules

Contains reusable SwiftUI components for delivering various types of survey questions. Each module represents a different input modality:
- **`NumericKeypad.swift`** – For numeric responses (e.g., hours of sleep).
- **`ScribbleDictation.swift`** – Allows text input via default kayboard, scribble or voice dictation.
- **`StepSlider.swift`** – A linear slider for selecting values on a discrete scale.
- **`CircularSlider.swift`** – A radial slider used for setting values (e.g., minutes of activity).
- **`ScrollableList.swift`** – Presents a scrollable list of options for categorical selection.

---

### 1.03.4. Utilities

These utility classes encapsulate essential logic for data flow, health tracking, and backend connectivity:
- **`SurveyManager.swift`** – Handles survey scheduling, state management, and delivery logic.
- **`StorageManager.swift`** – Manages local storage and data persistence on the watch.
- **`NotificationManager.swift`** – Schedules and responds to local push notifications for survey prompts.
- **`HealthKitManager.swift`** – Interfaces with HealthKit to collect step counts and other activity data.
- **`FirebaseManager.swift`** – Manages backend communication, syncing survey responses and metadata with Firebase.

---

You can explore and modify this project using **Xcode**, Apple’s official development environment for iOS and watchOS apps. After opening the `.xcodeproj` file, you’ll see the project navigator on the left side of the screen. This acts like a folder view: clicking the triangle next to each folder (e.g., `Views`, `SurveyModules`, `Utilities`) reveals the Swift files inside. Each file name corresponds to a specific part of the app, such as how surveys are displayed, stored, or synced with the backend.

If you're not familiar with coding, don’t worry. All customizations happen in very specific, clearly marked places. For example, to change a survey question, you can open one of the files inside the `SurveyModules` folder (like `NumericKeypad.swift`) and look for the comment that says something like `// Survey question`. Similarly, in the `SurveyManager.swift` file, you can find settings related to scheduling and triggering surveys. With a bit of guidance or template code, researchers and study designers can update many parts of the app without needing to write full Swift programs from scratch. Check next sections for each part's customization details.


[Back To Top](#top)
