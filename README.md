## ⌚️ DOSE: A Framework for Apple Watch-Based ESM/EMA & Health Data Collection

**DOSE** is a flexible framework for building your own Apple Watch app for real-time surveys and health data collection (no coding required).

Created for researchers, clinicians, and institutions, DOSE makes it easy to design and deploy wrist-based experience sampling and ecological momentary assessment (ESM/EMA) studies using **Apple Watches**. You can configure when and how prompts appear, what questions are asked, and which health metrics are collected, all through editable setup files.

The app also supports passive data collection through **HealthKit** and syncs both survey responses and health data securely to **Firebase Realtime Database** for easy access and analysis. Whether you're starting a small pilot or managing a large-scale study, DOSE gives you a complete toolkit to launch your own wrist-based research app—without needing to build everything from scratch.

What’s in this repo:
1. **Project files** — A watchOS-based ESM (Experience Sampling Method) data collection app you can customize for your own study.
2. **Step-by-step instructions** — Easy-to-follow guides to build and deploy the watch app through App Store.
3. **Comprehensive documentation** — Covers app features, data types collected, data collection flow, and participant onboarding prodcess.

---

### Documentation Structure

The documentation is organized into three main chapters, each tailored to a different phase of development and user expertise:

#### [Chapter 1: Overview of the DOSE Framework](https://github.com/iansulin/umich_dose/tree/main/Ch1_DOSE)
Learn about the DOSE app’s architecture, survey scheduling logic, data flow, and built-in interface templates.

- **What DOSE is for**: Built for Ecological Momentary Assessment (EMA) and Experience Sampling Method (ESM) studies.
- **Data flow**: From randomized survey scheduling to response capture and Firebase syncing.
- **Survey scheduling logic**: A local, window-based algorithm that sends notifications at randomized times throughout the day.
- **Prompt delivery and response logging**: Including expiration handling and offline fallback.
- **Built-in survey interfaces**: Five customizable UI templates optimized for quick, one-question responses.
- **Customization support**: Modify prompt text, survey type, input scales, and delivery timing to suit your study.


#### [Chapter 2: App Compilation and Customization Guide](https://github.com/iansulin/umich_dose/tree/main/Ch2_App%20Compilation)
Step-by-step guide for setting up Firebase, compiling the app, modifying survey content, customizing notifications, and configuring interface parameters.

- **Setting up your development environment** in Xcode.
- **Firebase configuration** for storing survey data and linking participant sessions.
- **Testing mode activation** for debugging notifications and survey logic.
- **Customizing notification schedules**, delivery windows, and prompt expiration rules.
- **Editing survey content**, response types, and user interface options.
- **Selecting the interface type** (slider, list, keypad, etc.) for each question in your study.
- **Uploading data securely and automatically** from watchOS to Firebase backend.

#### [Chapter 3: Source Code Reference Guide](https://github.com/iansulin/umich_dose/tree/main/Ch3_Source%20Guide)  
A technical walkthrough of the project’s source files—ideal for developers seeking deeper understanding or extending functionality.

- **File-by-file explanation** of the Swift source code in the Xcode project.
- **Logic for prompt randomization**, survey sequencing, and expiration behavior.
- **Data logging pipeline**: From local logs to Firebase uploads.
- **Notification management and app lifecycle handling**.
- **Extensibility tips** for adding more interfaces, data types (e.g., HealthKit), or integrating with other systems.

Whether you're a behavioral scientist launching your first ESM/EMA study, or an engineer building custom wearable data tools, DOSE offers a robust and elegant framework to build upon.

---

### Project Files

- All Releases:
  - [https://github.com/iansulin/stanford_screenomics/releases](https://github.com/iansulin/umich_dose/releases)
- Latest downloads:
  - [Download Most Recent Version DOSE App (ESM/EMA Data Collection App)](https://github.com/iansulin/umich_dose/releases/download/ESM-WatchApp/Dose-Watch-App_OpenSource-Release_072325.zip)
 
---

### Contact

For questions about the use of this project or collaboration inquiries, please contact:  
**Ian Kim** – iank@stanford.edu
