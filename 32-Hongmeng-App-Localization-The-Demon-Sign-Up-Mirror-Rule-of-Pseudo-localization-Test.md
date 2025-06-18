
# Hongmeng App Localization: The "Demon-Sign Up Mirror" Rule of Pseudo-localization Test

hello!I am Xiao L, the female programmer who "using pseudo-translation to find bugs" in Hongmeng application~ Do you know?A magical scene such as "button text is truncated into garbled code", "RTL language layout is inconsistent", and "date format is displayed as Martian text" may appear after formal translation!Today, let’s talk about how to use pseudo-localization testing to detect localization risks in advance~


## 1. Pseudo-localization test: "pressure preview" before localization
### (I) What is pseudo-localization?
Simply put, it is to simulate real localization using "fake translation". The core is to "deform" the text but not "change the meaning":
- **Length expansion**: Add the suffix to English words (such as `Hello`→`xHellox`), to simulate the German equal-length language
- **Character Replacement**: Insert special symbols (such as `Hello`→`Hello™`), test character set compatibility
- **Mirror Flip**: Simulate Arabic from right to left layout (RTL), check interface adaptation

### (II) Why do it have to be done?
| Problem Type | Pseudo-localization test discovery scenarios | Possible consequences of real localization |
|------------------|------------------------------------------|----------------------------------------|
| Layout overflow | Button text bloat causes truncation (such as `Settings`) | Button function is not available |
| Missing font | Blocks appear when displaying Japanese Kanna name | Users can't understand the interface |
| Function misalignment | The button position in RTL language is reversed (confirm → cancel interchange) | Data loss caused by user misoperation |
| Data format error | The US date format is displayed as `2023-13-32` | System logic crash |


## 2. The "three tricks" process of pseudo-localization testing
### (I) Step 1: Turn on "test mode"
```typescript
// Set pseudo-localization area (such as en-XA, X represents pseudo-test)
import { AppLocalizer } from '@ohos.app.ability.localization';
AppLocalizer.setLocale('en-XA'); // Trigger resource to load the pseudo-translated version
```

### (II) Step 2: "Brutality Test" interface
#### 1. **Layout "pressure resistance" test**
- Use tools to generate ultra-long pseudo-translated text (such as repeating 100 times `A`) and fill it into all text controls
- Check if it appears:
✔ Text truncation (showing `...`)
✔ Controls overlap (buttons overwrite input boxes)
✔ List item height exception

#### 2. **Character "compatibility" test**
- Insert special character combinations:
  ```
  €¥₹@#$%^&*()_+{}:"<>?|~`¬®©™αβγδεζηθικλμνξοπρστυφχψω
  ```  
- Check whether it is displayed as a square (□) or garbled code, and whether the font is automatically switched to the system default

#### 3. **RTL "mirror" test**
```typescript
// Force setting RTL layout (no real locale required)
import { AppLocalizer } from '@ohos.app.ability.localization';
AppLocalizer.setLayoutDirection(LayoutDirection.RIGHT_TO_LEFT);
```  
- observe:
✔ Whether the text direction is from right to left
✔ Whether the icon position is mirrored (such as the search icon moves from left to right)
✔ Is the order of table columns reversed?


## 3. Practical cases: The pseudo-localization of e-commerce apps "scene of crashing"
### (I) Scenario: Pseudo-localization test of product details page
#### 1. **Problem 1: Price tag truncation**
-Pre-pseudo-translation: `$99.99`
- Pseudo-translation: `x$99.99x` (Simulated German `Nintynine point ninety-nine Dollars`)
- **Phenomenon**: The price is displayed as `x$99.9...`, and the user cannot see the full price

#### 2. **Solution**
```xml
<Text
  ohos:id="$+id/price"
ohos:truncation_mode="none" <!-- truncation is prohibited -->
ohos:layout_weight="1" <!-- Elastic Placeholder -->
ohos:text_alignment="end" <!-- RTL language right-alignment -->
/>
```

### (II) Scenario: User Agreement Popup RTL Adaptation
#### 1. **Problem 2: The button order is inconsistent**
- After setting RTL by pseudo-localization, the "Agree" button is moved from right to left, which is reversed from the "Cancel" position

#### 2. **Solution**
```xml
<DirectionalLayout
  ohos:orientation="horizontal"
  ohos:layout_alignment="bottom"
  ohos:truncation_mode="none">
  
  <Button
    ohos:id="$+id/cancel"
    ohos:layout_weight="1"
ohos:is_rtl_support="true" <!-- Turn on RTL support -->
    ohos:text="$string:cancel" />
    
  <Button
    ohos:id="$+id/agree"
    ohos:layout_weight="1"
    ohos:is_rtl_support="true"
    ohos:text="$string:agree" />
</DirectionalLayout>
```


## 4. Pseudo-localized testing of "nuclear weapons" tools
### (I) Hongmeng’s own tools: Resource Changeer
1. **Function**: Generate pseudo-translation resource files with one click
2. **Usage**:
```bash
# Generate pseudo-localized version (en-XA) for zh-CN resources
hdc shell resource-changer -s zh-CN -d en-XA -o fake_res
```  
3. **Effect**:
```diff
- "buy_now": "Buy now"
+ "buy_now": "xl Buy x now"
```

### (II) Third-party tools: Lokalise
1. **Function**: Generate pseudo-translated text online (supports 50+ language rules)
2. **Example**:
Enter `Hello, World!` → Output `Hëllö, Wörld!` (Simulated German diacritics)

### (III) Automation test: Espresso
```java
// Write pseudo-localized test cases
@Test
public void testPseudoLocalization() {
// Set pseudo-localization area
    AppLocalizer.setLocale("en-XA");
    
// Check if the text contains pseudo-translation tags
    onView(withText(contains("x"))).check(matches(isDisplayed()));
    
// Check RTL layout
    onView(withId(R.id.button)).check(matches(withLayoutDirection(RIGHT_TO_LEFT)));
}
```


## 5. Pit avoidance guide: "Invalid operation" of pseudo-localization
### (I) "Surface Kung Fu" Trap
**Error practice**: Only test the main interface, ignore the secondary menu, pop-up window, toast
**Correct posture**:
- Cover all interface levels (main interface → level 3 menu → floating layer)
- Test all interaction paths (normal process → exception process → edge case)

### (II) Risk of "character set missing the network"
**Error practice**: Think ASCII character set covers all languages
**Correct posture**:
- Must-test character set:
  ```
Unicode scope:
- CJK Unified Ideotext (U+4E00-U+9FFF)
- Arabic characters (U+0600-U+06FF)
- Cyrillic characters (U+0400-U+04FF)
  ```  
- Tool: Use `Character.UnicodeBlock` to detect the block to which characters belong

### (III) Consequences of "performance neglect"
**Error practice**: Pseudo-localization tests do not focus on loading speed
**Correct posture**:
- Test the time to load the resource (whether the image/font becomes larger after pseudo-translation)
- Tools: Use `Systrace` to track resource loading threads


## 6. Future trends: AI-driven "intelligent pseudo-localization"
### (I) Pseudo-translation of "semantic perception"
AI analyzes the semantics of the source text and generates pseudo-translations that conform to the target language grammar (such as `Login`→`Anmeldung`→`xAnmeldungx`)

### (II) "Automatic layout repair"
The tool automatically identifies layout problems and generates fixes:
```mermaid
graph LR
A[Text overflow detected] --> B[AI suggests adding scroll view]
B --> C [Automatically modify XML layout file]
```

### (III) "Multi-device Cloud Test"
Simulate global 200+ devices/language combinations in the cloud, and automatically generate test reports


## Last reminder: The "Golden Rule" of Pseudo-localization
**Test coverage = (Number of interface elements × Language analog number) ÷ Number of problems missing**
- Interface elements: Must-test for each button/text box/icon
- Language simulation: At least 3 extreme cases tested (long text language/RTL language/special character language)
- Problem omission: Establish a "pseudo-localization problem library", comparing historical problems with each test
