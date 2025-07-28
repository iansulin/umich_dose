
## 1.04. Survey Interfaces

The DOSE Watch App provides **five customizable survey interface templates**, each designed specifically for the small screen and interaction constraints of the Apple Watch. These survey screens support Experience Sampling Method (ESM) and Ecological Momentary Assessment (EMA) data collection by allowing researchers to ask single-question prompts with intuitive, minimal user input. 

Each survey interface corresponds to a distinct type of question and response option/scale, such as sliders, multiple choice, or binary responses. At each notification, the app presents one survey randomly selected from a predefined list of surveys, enabling flexible and varied data collection within each prompt.

### Section List

- [1.04.1. Interface Templates](#1041-interface-templates)
- [1.04.2. Customizability](#1042-customizability)

---

### 1.04.1. Interface Templates

All screens are optimized for single-tap interactions and glanceable readability, ensuring minimal cognitive load and faster completion time on wrist-based devices. 

![Survey Interfaces](https://raw.githubusercontent.com/iansulin/umich_dose/main/Assets/Survey%20Interfaces.png "Survey Interfaces")

**Five built-in interfaces** are provided with easily customizable parameters: 
* (a) **Numeric Keypad**
* (b) **Scribble/Dictation**
* (c) **Step Slider**
* (d) **Circular Slider**
* (e) **Scrollable List**




| Interface Type | Description | Response Option/Scale | Example Survey Questionnaire | Example Response Option/Scale |
|-|-|-|-|-|
| **Numeric Keypad** | Allows users to input numeric responses via a keypad	| Entering a specific count or rating (e.g., 0-100) | "How many minutes ago did you last eat?" | "7.5" |
| **Scribble/Dictation** | Supports freeform text input via scribble, voice dictation, or the default keyboard | Free text, open-ended responses or notes | "Please describe your current mood." | "Feeling calm and relaxed." |
| **Step Slider** | A linear slider with discrete steps that snaps to predetermined points along the scale | Discrete numeric scales (e.g., 2, 4, ..., 10)| "On a scale of 1 to 10, how stressed are you?" | "8" |
| **Circular Slider** | A radial slider optimized for the watch’s crown, allowing smooth selection of values along a circular path | Continuous or fine-grained numeric values within a fixed range | "Rotate the dial to indicate the angle of sunlight exposure right now (0° to 360°)." | "135" |
| **Scrollable List** | A vertically scrollable list of options allowing single selection | Single-choice from predefined categories | "Select the activity you are doing right now." | "Walking" |

---

### 1.04.2. Customizability 

Researchers can easily adapt the built-in survey interfaces to suit their study needs. All survey configuration occurs directly in the source code.

You can customize:

- **Survey prompt text**: Replace the placeholder text `“Write your survey question here”` under the comment `// Survey question` with your desired item.
- **Response options or input scale**: Modify or expand the selectable options or input scale as needed.
- **Survey screen type**: Choose one of the five available survey templates based on the kind of data you wish to collect.
- **Number of questions**: Although each interface is designed to handle one question at a time, you may duplicate and chain screens if you wish to present a sequence of questions.

Further details on customization are provided in the _App Compilation_ chapter.







[Back to Top](#top)






