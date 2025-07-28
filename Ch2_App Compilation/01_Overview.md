


## 2.01. Overview: Using the DOSE Framework

This section guides you through how to set up and run the DOSE Apple Watch framework for collecting survey responses and health data. While you don’t need to write Swift code, you’ll need to use Xcode to open the project, adjust configuration files for your study (like surveys and schedules), and build the app onto a real Apple Watch. Whether you’re a researcher or part of a clinical team, this guide will walk you through the full process, step by step.

### Section List

- [2.01.1. Who This Is For](#2011-who-this-is-for)
- [2.01.2. Why Apple Watch?](#2012-why-apple-watch)
- [2.01.3. How the DOSE Framework Works?](#2013-how-the-dose-framework-works)
- [2.01.4. What You Will Learn](#2014-what-you-will-learn)

---

### 2.01.1. Who This Is For

The DOSE framework is designed for researchers, clinicians, and developers who want to collect real-time behavioral or health data using the Apple Watch. It's especially useful for those conducting experience sampling or ecological momentary assessment (ESM/EMA) studies, clinical trials, or behavioral interventions. Whether you're part of an academic lab, healthcare organization, or digital health startup, DOSE provides a customizable foundation to build your own wrist-based study app—without starting from scratch. Some familiarity with Xcode and Firebase setup is recommended, but you don’t need to be an experienced iOS developer to get started.

**Example Use Cases:** The DOSE framework can be adapted for a wide range of studies involving real-time or context-aware data collection. By editing the configuration files, you can tailor the content, frequency, and timing of prompts to suit your study’s goals. 

Examples include:
* **Mental health research** — delivering in-the-moment mood or stress surveys while passively tracking heart rate variability and sleep.
* **Physical activity studies** — combining step count and movement patterns with self-reported fatigue, motivation, or pain.
* **Medication adherence tracking** — prompting users to log medication use or symptoms at scheduled intervals.
* **Behavioral interventions** — sending prompts for mindfulness, cognitive reframing, or other micro-interventions based on time or activity.
* **Chronic disease monitoring** — collecting symptom ratings alongside physiological data like heart rate or respiratory rate.

---

### 2.01.2. Why Apple Watch?

Until recently, most open-source ESM/EMA tools were built for Android—largely because of its flexibility, open APIs, and fewer platform restrictions. However, many participants, especially in clinical and general population studies, use Apple devices, and the Apple Watch is often more acceptable for continuous wear than smartphones. Yet despite its popularity, the Apple Watch ecosystem lacked open, researcher-friendly tools for passive and active data collection. Additionally, watchOS does not natively support randomized prompt scheduling and enforces a hard limit of 64 scheduled notifications, which presents challenges for study designs requiring frequent or random prompts for an extended period.

The DOSE framework was created to fill this gap. The Apple Watch offers a powerful, underutilized platform for real-time surveys and passive sensing in daily life. Always worn on the wrist, it enables seamless, time-sensitive data capture with minimal burden on participants. Discreet haptic feedback and quick on-watch interactions support higher compliance with ESM/EMA prompts. Its built-in sensors—covering heart rate, activity, motion, and sleep—combined with HealthKit integration, make it ideal for capturing continuous, high-quality physiological data in ecologically valid settings. To overcome watchOS constraints, DOSE includes a custom scheduling engine that dynamically generates random survey times and refreshes the schedule daily to stay within the 64-notification limit—allowing flexible, long-term study designs without requiring manual intervention. DOSE makes this capability accessible to researchers without requiring deep expertise in Swift or iOS development.

---

### 2.01.3. How the DOSE Framework Works? 

The DOSE framework is a standalone Apple Watch app that enables real-time data collection through both passive sensing and active self-report. It is designed for researchers who want to run custom studies on Apple Watch without building an app from scratch. Study parameters—such as survey questions, scheduling, and Firebase database paths—are defined directly within the project files and can be adjusted by modifying the code.

**Key features of the DOSE system include:**

* **In-watch surveys**: The app displays scheduled ESM prompts directly on the Apple Watch using local notifications and a simple tap-based interface. Questions, options, and prompt timing are fully customizable in code.
* **Randomized prompt scheduling**: A custom RandomScheduler class selects survey prompt times randomly within specified time windows, enhancing ecological validity.
* **Notification management**: The framework dynamically manages scheduled notifications to work around watchOS’s limit of 64 active notifications.
* **HealthKit integration**: DOSE collects background physiological and activity data—such as heart rate, step count, and sleep—from HealthKit with user permission.
* **Local-first data handling**: All survey responses and sensor readings are first saved securely on the watch. The app then attempts to sync this data to your Firebase Realtime Database via REST API when a network connection becomes available.
* **Code-based study configuration**: Researchers modify the study setup by opening the Xcode project and editing predefined configuration lines (e.g., prompt schedule, Firebase URL, questions). No external config files or admin dashboard are used.
* **No iPhone or Firebase SDK dependency**: The app runs independently on the watch and interacts with Firebase purely through REST API calls—making it lightweight, portable, and easier to maintain.

---

### 2.01.4. What You Will Learn

In the following chapters, you’ll learn how to set up, customize, and deploy the DOSE framework to run your own Apple Watch–based ESM/EMA study. This guide is structured to support researchers, designers, and study coordinators—especially those without formal training in Swift or app development.

Specifically, you will learn:

* **How to customize the app** — such as defining your own survey questions, notification windows, and Firebase database paths directly in the code.
* **How to build and deploy the app** — using Xcode to install the app on a test or production Apple Watch.
* **How to collect and access data** — with data stored locally and automatically synced to your Firebase Realtime Database.

By the end, you’ll have a working Apple Watch app tailored to your study.




[Back to Top](#top)







