
# Hongmeng Next application globalization: Let users around the world experience your application "immersively"

hello!I am Xiao L, the female programmer who "plays language magic" in the Hongmeng application~ Do you know?A weather application that is suitable for 20 languages, displays humidity in Portuguese in Brazil, displays worship time in Saudi Arabia, and uses a wet date format in Japan... Today, let’s break down how Hongmeng makes the application "go to the local customs", embrace globalization in all aspects from code to design!


## 1. Internationalized "infrastructure": Make the code "compatible with all languages"
### (I) "Hard code" revolution
**Error demonstration**:
```typescript
// ❌ Hard-coded Chinese, unable to internationalize
button.setText("Buy Now");
```

**Correct posture**:
1. Definition in `resources/zh-CN/strings.json`:
```json
{
"buy_now": "Buy now"
}
```
2. Dynamic loading in the code:
```typescript
import { Resource } from '@ohos.resourceManager';
let resource = Resource.get();
button.setText(resource.getString('string:buy_now'));
```

### (II) "Adaptive Layout" secret
#### 1. Text length "elastic space"
- Use the flexGrow property of `DirectionalLayout` to automatically expand the text area
- RTL languages ​​such as Arabic need to set `textAlignment="right"` to
```xml
<DirectionalLayout 
  xmlns:ohos="http://schemas.huawei.com/res/ohos"
  ohos:orientation="vertical"
  ohos:layout_alignment="horizontal_center"
  ohos:padding="16vp">
  
  <Text 
    ohos:id="$+id:title"
    ohos:font_size="20sp"
    ohos:layout_weight="1"
ohos:text_alignment="start" <!-- Default is the starting point of LTR language -->
    ohos:truncation_mode="ellipsis_at_end"/>
</DirectionalLayout>
```

#### 2. Mirror layout "One-click switching"
```typescript
// Automatically switch mirroring according to language (RTL languages ​​such as Arabic and Hebrew)
import { AppLocalizer } from '@ohos.app.ability.localization';
let isRtl = AppLocalizer.getLayoutDirection() === LayoutDirection.RIGHT_TO_LEFT;
image.setPixelMap(isRtl ? "graphic/arrow_right" : "graphic/arrow_left");
```


## 2. Localized "soul": Let the application "understand culture and be down-to-earth"
### (I) "Time Magic" Four Quadrant
| Region | Date Format | Special Time Requirements |
|------------|-----------------------|-------------------------------|
| China | yyyy-MM-dd | Lunar solar term, Spring Festival countdown |
| United States | MM/dd/yyyy | Automatic adjustment of daylight saving time |
| Japan | Hwa (such as Reiwa X years) | Adult Day and Ullambana Festival reminder |
| Middle East | Islamic Calendar | Adjust push time during Ramadan |

**Code implementation**:
```typescript
import { intl } from '@kit.LocalizationKit';

function getLocalizedDate(locale: string) {
  let dateFormat = new intl.DateTimeFormat(locale, {
    year: 'numeric',
    month: '2-digit',
    day: '2-digit',
calendar: locale.includes('ja')? 'japanese' : 'gregorian' // Japanese usage and calendar
  });
  return dateFormat.format(new Date());
}
```

### (II) "Digital Culture" is very different
#### 1. "Precise Adaptation" of Currency Format
```typescript
let numberFormat = new intl.NumberFormat('en-US', {
  style: 'currency',
  currency: 'USD',
currencyDisplay: 'symbol' // Show $ symbol
});
priceText.setText(numberFormat.format(199.99)); // Show $199.99

// India uses Lakh/Crore units
let indianFormat = new intl.NumberFormat('en-IN', {
  maximumFractionDigits: 0,
  useGrouping: true
});
indianFormat.format(100000); // Show 1,00,000
```

#### 2. "Insensitivity Switch" of Weights and Measures
```typescript
import { I18NUtil } from '@ohos.i18n';

function convertTemperature(temp: number, fromUnit: string, toLocale: string) {
  let toUnit = toLocale.includes('us')? 'fahrenheit' : 'celsius';
  return I18NUtil.unitConvert(temp, fromUnit, toUnit);
}
convertTemperature(25, 'celsius', 'en-US'); // Output 77°F
```


## 3. Practical cases: The transformation of "Global Communication" of e-commerce applications
### (I) Scene Restore
- Target markets: China, the United States, Japan, Saudi Arabia
- Core Requirements:
✔ Multilingual interface (Simplified Chinese/English/Japanese/Arabic)
✔ Localized payment method (Alipay/credit card/cash on delivery)
✔ Holiday promotion adaptation (Double 11/Black Friday/Japanese Shopping Festival/Eida)

### (II) Key implementation
#### 1. Language switch "silk experience"
```typescript
//Storing user language preferences
function setAppLanguage(locale: string) {
  let config = new Configuration();
  config.locale = locale;
  AbilityManager.getAbility().setConfiguration(config);
// Refresh the interface
  mainWindow.recreate();
}

// List of supported languages
const SUPPORTED_LOCALES = ['zh-CN', 'en-US', 'ja-JP', 'ar-SA'];
```

#### 2. Culturally sensitive design
- **Saudi version**:
- Interface mirroring is RTL layout
- Disable alcohol product display
- Weekly time reminder (call local API to get)

- **Japan Version**:
- Date display and calendar
- Payment method is preferred to display convenience store payment
- The packaging pattern uses cherry blossoms and gentle elements

### (III) Test "Three Skills"
1. **Pseudo-localization test**:
Replace text with `Lorum Ipsum`+ special characters to check if the layout is truncated
   ```typescript
// Simulate long text test
   resource.addFakeString('string:app_name', 'A'.repeat(50)); 
   ```

2. **Cultural Consultant Review**:
Hire local staff in the target market and check:
- Color taboos (such as Saudi Arabia is not allowed to use yellow)
- Icon meaning (such as Japan uses lotus icon with caution)
- Holiday correctness

3. **Multiple device adaptation**:
Test the split screen display of RTL language on folding screen phones to ensure symmetric left and right layout


## 4. Performance optimization: Make international "lightly equipped"
### (I) "Load on demand" resources
- Download only the default language pack (such as English) for the first installation
- When users switch languages, download the corresponding resource package in the background
```typescript
async function loadLocaleResource(locale: string) {
  if (!hasResource(locale)) {
await downloadResourceFromServer(locale); // Download from CDN
registerResource(locale); // Register new language resources
  }
}
```

### (II) "Binary Compression" Solution
- Image resources are stored by region:
- European and American users load high saturation version
- Asian users load low saturation version
- Using `WebP` format, the compression rate is 30% higher than that of PNG


## 5. A guide to avoid pits: Globalization’s “reefs and shoals”
### (I) "Translation Ambiguity" Killer Plan
```typescript
// Use key-value pairs + comments to avoid ambiguity
// strings.json (English)
{
  "shopping_cart": "Shopping Cart",
  "empty_cart": "Your cart is empty. Start shopping now!"
}

// strings.json (French)
{
  "shopping_cart": "Panier d'achats",
  "empty_cart": "Votre panier est vide. Commencez à acheter maintenant!"
}
```

### (II) "Legal Compliance" Checklist
| Region | Compliance Points |
|------------|-----------------------------------|
| EU | GDPR data compliance, user consent is required |
| India | Prohibited display of beef-related products |
| Brazil | The postal code format is CEP, required |
| China | Application name must be registered, sensitive words are prohibited |

### (III) "Hot Update" Mechanism
```typescript
// Remotely update the translated text (no release required)
async function updateTranslations(locale: string, newStrings: Object) {
  let resourceManager = getResourceManager();
  resourceManager.updateResource(locale, 'strings', newStrings);
// Trigger interface refresh
  mainWindow.invalidate();
}
```


## 6. Future Evolution: The "Ultimate Form" of Globalization
### (I) "AI Real-time Translation"
The chat interface supports "voice input → real-time translation → target language display", covering more than 100 languages

### (II) "Cultural Perception" Engine
Automatically adapt through user geographic location and behavior data:
- Interface theme colors (Nordic prefers cool tones, Southeast Asia prefers warm tones)
- Recommended content (relevant products are pushed according to local festivals)

### (III) "Metauniverse Localization"
In Hongmeng Yuan service, implement:
- Virtual image clothing adapts to ethnic characteristics (such as Korean clothing and sari)
- Localization of virtual scene architectural style (Chinese garden/European castle)


## Last reminder: The "Golden Law" of Globalization
**User Experience = (Language Accuracy × Cultural Fitness) ÷ Loading Delay**
- Language accuracy: Professional translator proofreads to avoid hard injuries from machine flips
- Cultural fit: Hire local teams to participate in the design
- Loading delay: The first screen resource is compressed to less than 1MB, and CDN is deployed overseas
