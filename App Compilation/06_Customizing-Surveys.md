## 1.06. Customizing Surveys with `MainView.swift` and `SurveyModules`

The DOSE Apple Watch app delivers surveys by dynamically selecting and presenting one of several survey interfaces defined in the **SurveyModules** folder. This selection and presentation logic is centralized in the **`MainView.swift`** file, where the app determines which surveys are available and randomly chooses one to display when it is time to prompt the user.

**MainView’s Role in Survey Presentation**
- In **`MainView.swift`**, there is a key function called `getRandomSurveyView()` which maintains an array of all available survey views—each corresponding to a SwiftUI view struct in the SurveyModules folder, such as `survey_numericKeypad()`, `survey_scribbleDictation()`, etc. This function returns one survey view at random, which is then presented as a sheet for the user to complete.
- This design allows researchers to easily add or remove surveys by updating the list inside `getRandomSurveyView()` in `MainView.swift`. By controlling this list, you determine which surveys are eligible for random selection and display in the app.

**SurveyModules: Individual Survey Interfaces**
- Each survey interface in the `SurveyModules` folder is a standalone SwiftUI view representing a specific type of survey question (e.g., numeric keypad entry, scribble/dictation, slider, etc.). These views contain the question text, the input method, and the logic for submitting or dismissing a response.
- For example, `NumericKeypad.swift` defines the `survey_numericKeypad` view, which shows a numeric question with a keypad for user input. Each survey view manages its own UI and interaction but reports responses back to a shared `SurveyManager` object for saving and processing.


### Table of Contents

- [1.06.1. Selecting Survey Interfaces](#1061-customizing-which-surveys-appear-in-mainviewswift)
- [1.06.2. Customizing the Numeric Keypad Survey Interface](#1062-customizing-the-numeric-keypad-survey-interface)
- [1.06.3. Customizing the Scribble/Dictation Survey Interface](#1063-customizing-the-scribbledictation-survey-interface)
- [1.06.4. Customizing the Step Slider Survey Interface](#1064-customizing-the-step-slider-survey-interface)
- [1.06.5. Customizing the Circular Slider Survey Interface](#1065-customizing-the-circular-slider-survey-interface)
- [1.06.5. Scrollable List Interface](#1065-scrollable-list-interface)
- [1.06.6. Adding a New Survey Interface to the App](#1066-adding-a-new-survey-interface-to-the-app)


---

### 1.06.1. Customizing Which Surveys Appear in `MainView.swift`

```swift
// ============================================================================================= //
// Function to select a random survey view from an array of available views.                     //
// Customize number of surveys.                                                                  //    
func getRandomSurveyView() -> AnyView {
  let views: [AnyView] = [
    AnyView(survey_numericKeypad()),
    AnyView(survey_scribbleDictation()),
    AnyView(survey_stepSlider()),
    AnyView(survey_circularSlider()),
    AnyView(survey_scrollableList())
  ]
        
  // Return a randomly selected view from the array.
  return views.randomElement() ?? AnyView(EmptyView())
}
// ============================================================================================= //
 ```

**To remove or disable a survey:**
By default, the framework includes the five survey interfaces, shown in the `AnyView` array: `survey_numericKeypad()`, `survey_scribbleDictation()`, `survey_stepSlider()`, `survey_circularSlider()`, `survey_scrollableList()`; These represent five different ways participants can answer survey questions. However, _you are not required to use all of them._

* **To use only a subset of interfaces**, simply **delete or comment out** the ones you don't want to include. For example, if you only want to remove `survey_scribbleDictation()` and `survey_circularSlider()` and use three of the five (if you want the app to randomly pick between just the remaining three interfaces):
```swift
let views: [AnyView] = [
    AnyView(survey_numericKeypad()),
    AnyView(survey_stepSlider()),
    AnyView(survey_scrollableList())
]
```

* If your study design requires **only a single, fixed survey type** (for example, to ensure standardization across all participants or to repeatedly administer a validated question format), you can simplify the array to include just that one interface. Since there is only one view in the array, the random selection will always return it, ensuring consistent survey delivery across all notifications:

```swift
let views: [AnyView] = [
    AnyView(survey_numericKeypad())
]
```
> This setup is ideal for studies prioritizing measurement consistency or testing a specific question format repeatedly over time.

---

### 1.06.2. Customizing the Numeric Keypad Survey Interface

The **Numeric Keypad** interface is useful when you want participants to respond using numbers, such as reporting time spent moving, hours of sleep, or pain level.

The file you will edit is:
```
SurveyModules/NumericKeypad.swift
```

* What You Can Customize in `NumericKeypad.swift`:
Each survey interface is designed to keep customizations straightforward, and the **Numeric Keypad** module is no exception. This interface allows participants to enter a numeric response using a watch-optimized keypad, ideal for logging things like minutes, hours, or counts.

In the `NumericKeypad.swift` file, you can customize the following parts:
| Section |	What It Controls |
|-|-|
| `surveyIdentifier` | A unique internal label used to tag and store this specific survey's responses. |
| **Survey Question Text** | The actual question text shown to the participant on the watch display. | 
| **Placeholder Text** | The grayed-out hint text inside the number input box, guiding expected input format. | 

You **do not need to edit** layout, button logic, or data-saving code — these are already handled by the framework unless you have advanced needs.

#### a. **`surveyIdentifier`** 

This string uniquely labels the response for this specific survey and used for **tracking in Firebase**. It helps you identify the source of the response in your backend or analytics.
> When the participant submits or dismisses a survey, the `surveyIdentifier` is saved using the `SurveyManager` and later uploaded to Firebase. It appears in your data as a field that tells you **which survey was shown**.

* **Where to find it:**
```swift
// ============================================================================================= //
// Survey identifier                                                                             //
let surveyIdentifier: String = "Q1. Numeric Keypad"
// ============================================================================================= //
```

* **To customize:** Replace the text with a short, descriptive label for your question.
> Keep it short and consistent. Avoid spaces or special characters if you're exporting this data.

* **Example customization:**
```swift
let surveyIdentifier: String = "Q1. Physical Activity Minutes"
```

* **Example Firebase entry:**
```json
{
  "surveyNo": "Q1. Physical Activity Minutes",
  "response": "25",
  "timestamp": "2025-07-27T14:03:12Z"
}
```
> Choose identifiers that are unique and consistent. They help later during data cleaning and analysis, especially if you reuse similar question types across different studies or weeks. 

#### b. **Survey Question Text** 

This is the main question shown to the participant on the watch face.

* **Where to find it:**
```swift
// ============================================================================================= //
// Survey question                                                                               //
Text("Write your survey question here.")
// e.g. Text("In the past 2 hours, how many MINUTES did you spend MOVING?")                      //
// ============================================================================================= //
```

* **To customize:** Replace the placeholder text with your actual question.
```swift
Text("In the past 2 hours, how many minutes were you physically active?")
```
> Keep the question concise — long text may not display well on the small watch screen.

c. **Placeholder Text (Optional):** This is the light gray text inside the number entry field, providing a hint to the user.

* **Where to find it:**
```swift
DigiTextView(placeholder: "Type a Minute", ...)
```
* **To customize:** Update the placeholder to match the expected input.
```swift
DigiTextView(placeholder: "Enter minutes", ...)
```
> Use clear input hints like “Enter minutes” or “Type a number” depending on your question.

---

### 1.06.3. Customizing the Scribble/Dictation Survey Interface

The **Scribble/Dictation** interface is ideal when your study requires open-ended text input, such as mood descriptions, symptom reporting, or qualitative feedback. It supports multiple input methods, including **dictation**, **scribble**, and the **default QWERTY keyboard**, making it versatile and user-friendly on Apple Watch.

The file you will edit is:
```
SurveyModules/ScribbleDictation.swift
```

* What You Can Customize in `ScribbleDictation.swift`:
This survey interface is structured to keep core customization points easy to locate and edit. You can adjust:

| Section                  | What It Controls                                                                 |
|--------------------------|----------------------------------------------------------------------------------|
| `surveyIdentifier`       | A unique internal label used to tag and store this survey's responses.          |
| **Survey Question Text** | The main question shown to the participant.                                     |
| **Placeholder Text**     | Light gray text inside the input box, hinting at what type of answer is expected. |

The rest of the UI — including input handling, buttons, and Firebase upload — is already configured. You do **not need to modify** layout, navigation, or submission logic unless you have advanced needs.

#### a. `surveyIdentifier`

This string uniquely labels the response for this specific survey and is used for **tracking in Firebase**.

> When a participant submits or dismisses a response, this identifier is saved and later uploaded to Firebase. It helps you know which question was presented.

* **Where to find it:**
```swift
// ============================================================================================= //
// Survey identifier                                                                             //
let surveyIdentifier: String = "Q2. Scribble/Dictation"
// ============================================================================================= //
```

* **To customize:** Replace the string with a short, descriptive label for your question.
> Keep it consistent and simple. Avoid spaces or special characters for cleaner export and parsing.

* **Example customization:**
```swift
let surveyIdentifier: String = "Q2.MoodLog"
```

* **Example Firebase entry:**
```json
{
  "surveyNo": "Q2.MoodLog",
  "response": "Feeling anxious and tired.",
  "timestamp": "2025-07-27T14:17:49Z"
}
```

#### b. Survey Question Text
This is the prompt shown to the participant on the watch screen. Keep it short so it fits the small display well.

* **Where to find it:**
```swift
// ============================================================================================= //
// Survey question                                                                               //
Text("Write your survey question here.")
// e.g. Text("How would you describe your mood right now?")                                     //
// ============================================================================================= //
```

* **To customize:** Replace the placeholder with your study question.

* **Example customization:**
```swift
Text("How would you describe your mood right now?")
```
> Avoid multiline sentences. Keep the question under ~10 words when possible.

#### c. Placeholder Text (Optional)
This gray text appears in the input field before the participant types or speaks a response. It helps guide them on what type of input is expected.

* **Where to find it:**
```swift
TextField("Type a Response", text: $responseValue)
```

* **To customize:** Replace `"Type a Response"` with a brief input hint.

* **Example customization:**
```swift
TextField("Describe your mood", text: $responseValue)
```
> Use this to suggest tone or format: e.g., “One or two words”, “Describe symptoms”, etc.

---

### 1.06.4. Customizing the Step Slider Survey Interface

The **Step Slider** interface is ideal when your study requires participants to provide numeric ratings using a simple, intuitive scale. It is well-suited for Apple Watch, enabling input via digital crown or touch to select a discrete value (e.g., stress level, energy rating, minutes of activity).

The file you will edit is:
```
`SurveyModules/StepSlider.swift`
```

* What You Can Customize in `StepSlider.swift`:
This survey interface is structured to keep core customization points easy to locate and edit. You can adjust:

| Section | What It Controls |
|-|-|
| `surveyIdentifier` | A unique internal label used to tag and store this survey's responses. |
| **Survey Question Text** | The main question shown to the participant. |
| **Slider Range** | Minimum and maximum allowed values on the slider. |
| **Slider Step Size** | Increment between selectable values. |
| **Initial Displa Value** | Default value shown when the screen loads. |

The rest of the UI — including input handling, buttons, and Firebase upload — is already configured. You do **not need to modify** layout, navigation, or submission logic unless you have advanced needs.

#### a. `surveyIdentifier`

This string uniquely labels the response for this specific survey and is used for **tracking in Firebase**.

> When a participant submits or dismisses a response, this identifier is saved and later uploaded to Firebase. It helps you know which question was presented.

* **Where to find it:**
```swift
// ============================================================================================= //
// Survey identifier                                                                             //
let surveyIdentifier: String = "Q3. Step Slider"
// ============================================================================================= //
```

* **To customize:** Replace the string with a short, descriptive label for your question.

> Keep it consistent and simple. Avoid spaces or special characters for cleaner export and parsing.

* **Example customization:**
```swift
let surveyIdentifier: String = "Q3.EnergyLevel"
```

* **Example Firebase entry:**
```json
{
  "surveyNo": "Q3.EnergyLevel",
  "response": "7",
  "timestamp": "2025-07-27T14:21:03Z"
}
```

#### b. Survey Question Text
This is the prompt shown to the participant on the watch screen. Keep it short so it fits the small display well.

* **Where to find it:**
```swift
// ============================================================================================= //
// Survey question                                                                               //
Text("Write your survey question here.")
// e.g. Text("On a scale from 1 to 10, how stressed are you right now?")                        //
// ============================================================================================= //
```

* **To customize:** Replace the placeholder with your study question.

* **Example customization:**
```swift
Text("On a scale from 1 to 10, how stressed are you right now?")
```
> Keep the question concise — ideally under 10–12 words — to fit comfortably on screen.


#### c. Stepper Configuration (Slider Range and Step Size)

This section defines how the participant selects a numeric response using the Digital Crown or finger. You can configure the **minimum and maximum values** and the **step interval** (how much the value increases or decreases with each movement).

* **Where to find it:**
```swift
// Survey response
                
// ============================================================================================= //
//                                                                                               //
// Stepper configuration                                                                         //
//                                                                                               //
Slider(value: $value, in: 0...120, step: 5)
// in: 0...120 defines the range of allowed values
// step: 5 sets the step increment
// e.g. Slider(value: $value, in: 0...60, step: 15)
//                                                                                               //
// ============================================================================================= //
```

* **How It Works**

| Parameter           | Description                                                                 |
|---------------------|-----------------------------------------------------------------------------|
| `value: $value`     | A variable bound to the current value of the slider (updated in real time). |
| `in: min...max`     | Defines the **minimum and maximum** values allowed for selection.            |
| `step:`             | Sets how much the value should increment or decrement with each swipe.       |

* **To customize:**
  * Change in: 0...120 to your desired minimum and maximum.
  * Change step: 5 to adjust the interval between allowed values.

**Example customization:**
```swift
Slider(value: $value, in: 0...60, step: 15)

```
> This allows the user to select 0, 15, 30, 45, or 60 — ideal for reporting time or frequency.

> Smaller step sizes allow finer-grained responses; larger step sizes simplify user interaction.

| Example Code                             | Scenario Description                                      |
|----------------------------------------|-----------------------------------------------------------|
| `Slider(value: $value, in: 0...120, step: 5)`  | Select minutes spent exercising (0 to 120 minutes, 5-min increments) |
| `Slider(value: $value, in: 10...50, step: 10)` | Select age range for a group (from 10 to 50 years, steps of 10)     |
| `Slider(value: $value, in: 1...10, step: 1)`   | Rate pain level on a scale from 1 to 10, steps of 1                |
| `Slider(value: $value, in: 30...90, step: 15)` | Choose sleep duration between 30 and 90 minutes, increments of 15  |



#### d. Initial Display Value (Optional)

Controls what number is selected when the survey screen first appears.

* **Where to find it:**
```swift
// ============================================================================================= //
// Initial display value
@State private var value = 0.0
// ============================================================================================= //
```

* **To customize:** Set this to a midpoint or expected default value.

* **Example customization:**
```swift
@State private var value = 30.0
```
This does not limit the range — it only affects the initial display.

---

### 1.06.5. Customizing the Circular Slider Survey Interface

The **Circular Slider** interface lets participants select a numeric response by rotating a knob along a circular track, ideal for capturing continuous values like duration, intensity, or quantity within a predefined range.

The file you will edit is:

```
SurveyModules/CircularSlider.swift
```

* What You Can Customize in `CircularSlider.swift`:
This module is designed for easy adjustments to fit your study’s needs. You can customize:

| Section	| What It Controls |
|-|-|
| `surveyIdentifier` |	A unique internal label tagging responses for this survey (used in Firebase). |
| **Survey Question Text** | The prompt shown on the watch face to guide participant input. |
| **Slider Configuration (`Config2`)** |	Defines the numeric range and total scale for the circular slider, controlling minimum, maximum, and full rotation mapping. |

You do **not** need to modify the UI layout, button logic, or Firebase-saving mechanisms unless you want advanced custom behavior.

#### a. `surveyIdentifier`
This string uniquely identifies the survey and is saved with each response for backend tracking.
> When a participant submits or dismisses a survey, this label is saved and uploaded to Firebase to help identify which question was shown.

* **Where to find it:**
```swift
// ============================================================================================= //
// Survey identifier                                                                             //
init(surveyIdentifier: String = "Q4. Circular Slider",
// ============================================================================================= //
```

* **To customize:** Replace with a descriptive label consistent across your dataset.

* **Example customization:**
```swift
let surveyIdentifier: String = "Q4. Exercise Duration"
```

* **Example Firebase entry:**
```json
{
  "surveyNo": "Q4. Exercise Duration",
  "response": "45.0",
  "timestamp": "2025-07-27T14:30:00Z"
}
```

#### b. Survey Question Text
The prompt shown to participants above the circular slider.

* **Where to find it:**
```swift
// ============================================================================================= //
// Survey question                                                                               //
Text("Write your survey question here.")                                                         //
// e.g. Text("In the past 2 hours, how many MINUTES did you spend MOVING?")                      //
// ============================================================================================= //
```

* **To customize:** Replace the placeholder with your actual study question.
* **Example customization:**
```swift
Text("How many minutes did you exercise today?")
```
> Keep it concise to fit the small watch display. Avoid long sentences.

#### c. Slider Configuration (`Config2`)
This structure defines the numeric range and behavior of the circular slider:
```swift
struct Config2 {
    let minimumValue: CGFloat
    let maximumValue: CGFloat
    let totalValue: CGFloat
}
```

* `minimumValue`: The lowest allowed response (slider start).
* `maximumValue`: The highest allowed response (slider end, usually slightly above totalValue).
* `totalValue`: The total scale mapped to the full 360° rotation of the slider.

* **Where to find it:**
```swift
// ============================================================================================= //
//                                                                                               //
// Circular slider configuration                                                                 //
//                                                                                               //
     config: Config2 = Config2(minimumValue: 0, maximumValue: 181, totalValue: 180)
// e.g. init(config: Config2 = Config2(minimumValue: 30, maximumValue: 61, totalValue: 60)) {    //
//                                                                                               //
// minimumValue: the lowest value the slider can show                                            //
// maximumValue: the upper limit for response selection (usually set just above totalValue)      //
// totalValue: the total range of values that maps the full 360 degrees rotation of the slider   //
//                                                                                               //
// ============================================================================================= //
```

* **To customize:** Adjust these values based on your response range needs.

**Example Scenarios for Circular Slider Configuration**

| Configuration Code Example                                          | Use Case Description                                             |
|----------------------------------------------------------------------|------------------------------------------------------------------|
| `Config2(minimumValue: 0, maximumValue: 181, totalValue: 180)`      | Measure total minutes of physical activity (0–180 minutes)       |
| `Config2(minimumValue: 30, maximumValue: 61, totalValue: 60)`       | Track nap duration for participants who nap 30–60 minutes        |
| `Config2(minimumValue: 1, maximumValue: 11, totalValue: 10)`        | Rate pain or exertion on a 1–10 scale                            |
| `Config2(minimumValue: 50, maximumValue: 101, totalValue: 50)`      | Measure heart rate zones from 50 to 100 bpm                      |
| `Config2(minimumValue: 5, maximumValue: 66, totalValue: 60)`        | Log servings of food items from 5 to 65 servings                |
| `Config2(minimumValue: 10, maximumValue: 121, totalValue: 110)`     | Track time spent standing between 10 and 120 minutes             |

#### d. How the Slider Works Internally
* The circular slider converts the user's drag location into an angle (in radians).
* The angle is normalized from 0° to 360°, then mapped to a numeric value based on `totalValue`.
* Only values between `minimumValue` and `maximumValue` are accepted.
* The current value is displayed as a number inside the slider circle.
* Participants adjust the knob by dragging it around the circle.

---

### 1.06.5. Scrollable List Interface

The **Scrollable List** interface is best used when participants need to select a response from a predefined list of _discrete options_, such as frequency ratings or categorical choices. The Apple Watch’s scrollable picker allows fast and precise selection.

The file you will edit is:
```
SurveyModules/ScrollableList.swift
```

* What You Can Customize in `ScrollableList.swift`:
This interface is designed for easy customization, allowing you to modify key parts such as:

| Section                      | What It Controls                                                       |
| ---------------------------- | ---------------------------------------------------------------------- |
| `surveyIdentifier`           | A unique internal label used to tag and store this survey's responses. |
| **Survey Question Text**     | The main question shown to the participant.                            |
| **Picker Options**           | The list of selectable items shown in the scrollable picker.           |
| **Default Picker Selection** | Which option is initially selected when the survey loads.              |

The rest of the logic — including button behavior, layout, and response saving — is fully configured. You do **not need to modify** navigation, submission, or Firebase logic unless you have advanced needs.

#### a. `surveyIdentifier`
This uniquely identifies the current survey and is included in all saved responses.
> Used by Firebase to associate each submission with the correct question.

* **Where to find it:**
```swift
// ============================================================================================= //
// Survey identifier                                                                             //
let surveyIdentifier: String = "Q5. Scrollable List"
// ============================================================================================= //
```

* **To customize:** Replace the string with a short, unique identifier for the question.

* **Example customization:**
```swift
let surveyIdentifier: String = "Q5.SymptomFrequency"
```

* **Example Firebase entry:**
```json
{
  "surveyNo": "Q5.SymptomFrequency",
  "response": "Often",
  "timestamp": "2025-07-28T10:52:31Z"
}
```

#### b. Survey Question Text
This is the question the participant will read before selecting a response.

* **Where to find it:**
```swift
// ============================================================================================= //
// Survey question                                                                               //
Text("Write your survey question here.")                                                         //
// e.g. Text("In the past 2 hours, how many MINUTES did you spend MOVING?")                      //
// ============================================================================================= //
```

* **To customize:** Replace the placeholder with your actual survey item.
* **Example customization:**
```swift
Text("How often did you feel anxious today?")
```
> Keep your question concise (ideally <12 words) for optimal display on the small screen.

#### c. Picker Configuration (Options + Initial Selection)
The scrollable picker is defined by an array of strings and a default selected value. Users scroll to choose from these items.

* **Where to find it:**
```swift
// ============================================================================================= //
//                                                                                               //
// Picker configuration                                                                          //
//                                                                                               //
var responseOptions = ["Option 1", "Option 2", "Option 3"] // An array of response options       //
// e.g. var responseOptions = ["0", "15", "30", "45", "60"]                                      //
//                                                                                               //
@State private var defaultResponse = "Option 1" // An initial selection                          //
// e.g. @State private var defaultResponse = "30"                                                //
// It must match one of the strings in responseOptions,                                          //
// or the picker will default to the first one visually.                                         //
//                                                                                               //
// ============================================================================================= //
```

* **How It Works**

| Component         | Description                                                                 |
| ----------------- | --------------------------------------------------------------------------- |
| `responseOptions` | An array of strings shown in the scrollable picker.                         |
| `defaultResponse` | The item pre-selected when the view appears. Must match one of the options. |

* **To customize:**
  * Replace responseOptions with a custom list of options.
  * Update defaultResponse to match one of the options exactly.

* **Example customization:**
```swift
var responseOptions = ["Never", "Rarely", "Sometimes", "Often", "Always"]
@State private var defaultResponse = "Sometimes"
```

> The default response must match **exactly** (case-sensitive) one of the strings in `responseOptions`, or the picker may default visually to the first item.

* **Example Scenarios**

| Scenario                            | Example `responseOptions`                             | Default       |
| ----------------------------------- | ----------------------------------------------------- | ------------- |
| Frequency of symptom                | `["Never", "Rarely", "Sometimes", "Often", "Always"]` | `"Sometimes"` |
| Categorical mood rating             | `["Happy", "Neutral", "Sad", "Angry"]`                | `"Neutral"`   |
| Energy level (text-based scale)     | `["Low", "Moderate", "High"]`                         | `"Moderate"`  |
| Time spent on activity (in minutes) | `["0", "15", "30", "45", "60"]`                       | `"30"`        |
| Number of medications taken         | `["0", "1", "2", "3 or more"]`                        | `"0"`         |


---

### 1.06.6 Adding a New Survey Interface to the App

To include a new custom survey screen in the app, follow these two steps:

1. **Create a new Swift file** for the survey view in the `SurveyModules` folder.
2. **Register the view** inside the `views` array in the survey manager, so it becomes part of the active survey sequence.

This modular setup allows you to plug in new survey interfaces without changing the app’s navigation or layout logic.

#### Step 1. Create the New Survey View File
All surveys are self-contained SwiftUI views. Your new file should follow this naming pattern and structure:

* **Location:**
```
SurveyModules/survey_newSurvey.swift
```
* **File Template:**
```swift
//
//  survey_newSurvey.swift
//  dose Watch App
//

import SwiftUI

struct survey_newSurvey: View {
    let surveyIdentifier: String = "Q6.NewSurvey"

    @EnvironmentObject var surveyManager: SurveyManager
    var watchDisplayHeight: CGFloat = WKInterfaceDevice.current().screenBounds.height
    var watchDisplayWidth: CGFloat = WKInterfaceDevice.current().screenBounds.width

    @State private var response: String = ""

    var body: some View {
        VStack {
            Spacer()
            
            Text("Write your survey question here.")
                .font(.system(size: watchDisplayWidth/13))
                .frame(height: watchDisplayHeight/3)

            TextField("Type your response", text: $response)
                .multilineTextAlignment(.center)
                .frame(height: 40)

            HStack {
                // Dismiss Button
                Button {
                    surveyManager.surveyNo = surveyIdentifier
                    surveyManager.response = "Dismissed"
                    surveyManager.saveSurvey()
                    SurveyManager.shared.showSurveySheet = false
                } label: {
                    Image(systemName: "x.circle.fill")
                        .foregroundColor(.red)
                        .font(.system(size: watchDisplayWidth/5))
                }
                .buttonStyle(PlainButtonStyle())

                Spacer()

                // Submit Button
                Button {
                    surveyManager.surveyNo = surveyIdentifier
                    surveyManager.response = response
                    surveyManager.saveSurvey()
                    SurveyManager.shared.showSurveySheet = false
                } label: {
                    Image(systemName: "checkmark.circle.fill")
                        .foregroundColor(.green)
                        .font(.system(size: watchDisplayWidth/5))
                }
                .buttonStyle(PlainButtonStyle())
            }
            .padding()
            Spacer()
        }
    }
}
```
> You may reuse or adapt any of the layout templates from other modules like `StepSlider.swift`, `ScrollableList.swift`, etc.

#### Step 2. Register the New View in the Survey Flow

To activate your new survey screen, you need to **register it inside the `views` array in `MainView.swift`**. This array determines the order in which surveys are shown to the participant.

* **File:**
```
MainView.swift
```

* **Locate this array:**
```swift
// ============================================================================================= //
// Function to select a random survey view from an array of available views.                     //
// Customize number of surveys.                                                                  //
    
func getRandomSurveyView() -> AnyView {
  let views: [AnyView] = [
    AnyView(survey_stepSlider()),
    AnyView(survey_scrollableList()),
    // Add your new survey view below
  ]
        
  // Return a randomly selected view from the array.
  return views.randomElement() ?? AnyView(EmptyView())
}
// ============================================================================================= //
```

* **Add your new survey:**
```swift
  let views: [AnyView] = [
    AnyView(survey_stepSlider()),
    AnyView(survey_scrollableList()),
    AnyView(survey_newSurvey()) // Your new custom survey view
  ]
```

The app randomly selects one survey from the views array each time it is triggered. You can control which surveys are active by adding or removing them from the array.

> **Important**: Make sure any survey you add here matches a SwiftUI view struct implemented in the `SurveyModules` folder.



[Back To Top](#top)
