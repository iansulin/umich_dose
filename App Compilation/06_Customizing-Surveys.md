## 1.06. Customizing Surveys with `MainView.swift` and `SurveyModules`

The DOSE Apple Watch app delivers surveys by dynamically selecting and presenting one of several survey interfaces defined in the **SurveyModules** folder. This selection and presentation logic is centralized in the **`MainView.swift`** file, where the app determines which surveys are available and randomly chooses one to display when it is time to prompt the user.

---

**MainView’s Role in Survey Presentation**
- In **`MainView.swift`**, there is a key function called `getRandomSurveyView()` which maintains an array of all available survey views—each corresponding to a SwiftUI view struct in the SurveyModules folder, such as `survey_numericKeypad()`, `survey_scribbleDictation()`, etc. This function returns one survey view at random, which is then presented as a sheet for the user to complete.
- This design allows researchers to easily add or remove surveys by updating the list inside `getRandomSurveyView()` in `MainView.swift`. By controlling this list, you determine which surveys are eligible for random selection and display in the app.

**SurveyModules: Individual Survey Interfaces**
- Each survey interface in the `SurveyModules` folder is a standalone SwiftUI view representing a specific type of survey question (e.g., numeric keypad entry, scribble/dictation, slider, etc.). These views contain the question text, the input method, and the logic for submitting or dismissing a response.
- For example, `NumericKeypad.swift` defines the `survey_numericKeypad` view, which shows a numeric question with a keypad for user input. Each survey view manages its own UI and interaction but reports responses back to a shared `SurveyManager` object for saving and processing.

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













[Back To Top](#top)
