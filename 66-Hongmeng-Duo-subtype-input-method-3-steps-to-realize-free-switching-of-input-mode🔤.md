
# Hongmeng Duo subtype input method: 3 steps to realize free switching of input mode🔤

The core of implementing multilingual/mode switching in the Hongmeng input method is the flexible configuration and dynamic loading of subtypes.This article uses the simplest process to help you master the core logic~


## 1. Sub-type basics: Define multi-modal "digital identity" 🆔
### 1. Subtype configuration file (input_method_config.json)
```json  
{  
  "subtypes": [  
    {  
"id": "en_us", // Unique ID
"label": "English", // Show name
"locale": "en-US", // Language area
"mode": "qwerty", // Keyboard mode
"icon": "$media:en_icon" // Toggle icon
    },  
    {  
      "id": "zh_cn",  
"label": "Chinese",
      "locale": "zh-CN",  
      "mode": "pinyin",  
      "icon": "$media:cn_icon"  
    }  
  ]  
}  
```  

### 2. module.json5 registration
```json  
{  
  "extensionAbilities": [  
    {  
      "type": "inputMethod",  
      "metadata": [  
        {  
          "name": "ohos.extension.input_method",  
"resource": "$profile:input_method_config" // Associate configuration file
        }  
      ]  
    }  
  ]  
}  
```  


## 2. Interface switching: dynamically load different keyboard layouts🎹
### 1. Listen to subtype change events
```typescript  
inputMethodAbility.on('setSubtype', (subtype) => {  
  switch (subtype.id) {  
    case 'en_us':  
this.loadEnglishKeyboard(); // Load English layout
      break;  
    case 'zh_cn':  
this.loadChineseKeyboard(); // Load Chinese layout
      break;  
  }  
});  
```  

### 2. Multi-layout implementation (ArkUI example)
```typescript  
// English keyboard layout
private loadEnglishKeyboard() {  
  this.panel.setUiContent(() => {  
    Grid() {  
      ForEach(englishKeys, (key) => {  
        Button(key.char)  
          .width(40)  
          .height(40)  
          .onClick(() => this.insertText(key.char));  
      });  
    }  
  });  
}  

// Chinese keyboard layout
private loadChineseKeyboard() {  
  this.panel.setUiContent(() => {  
    Grid() {  
      ForEach(chineseKeys, (key) => {  
        Button(key.pinyin)  
          .width(50)  
          .height(50)  
          .onClick(() => this.searchPinyin(key.pinyin));  
      });  
// Candidate word column
      Row() {  
        ForEach(candidates, (word) => {  
          Text(word).onClick(() => this.commitText(word));  
        });  
      }  
    }  
  });  
}  
```  


## 3. Switching logic: User trigger and automatic adaptation 🤖
### 1. Manually switch (button click)
```typescript  
// Toggle button click event
async switchSubtype(targetId: string) {  
  const subtypes = await inputMethod.getSetting().listCurrentInputMethodSubtype();  
  const targetSubtype = subtypes.find(s => s.id === targetId);  
  if (targetSubtype) {  
    await inputMethod.switchCurrentInputMethodSubtype(targetSubtype);  
  }  
}  
```  

### 2. Automatic switch (according to input content)
```typescript  
// Automatically switch when English input is detected
private detectLanguage(text: string) {  
  const isEnglish = /^[a-zA-Z]+$/.test(text);  
  if (isEnglish && this.currentSubtype.id !== 'en_us') {  
    this.switchSubtype('en_us');  
  } else if (!isEnglish && this.currentSubtype.id !== 'zh_cn') {  
    this.switchSubtype('zh_cn');  
  }  
}  
```  


## 4. Optimization skills: Make the switch more "feelless"✨
### 1. Preload layout resources
```typescript  
// Preload all subtype layouts in onCreate
private preloadLayouts() {  
this.loadEnglishKeyboard(); // Render English layout in advance
this.loadChineseKeyboard(); // Render the Chinese layout in advance
}  
```  

### 2. Animation transition effect
```typescript  
// Add gradient animation when switching layout
panel.setUiContent(() => {  
  Column() {  
// Current layout
    if (currentSubtype === 'en_us') EnglishKeyboard()  
    else ChineseKeyboard()  
}.animate({ type: 'opacity', duration: 300 }); // 300ms gradient
});  
```  


## Summary: The "Three Elements" of Sub-Type Development
1. **Configuration first**: Define all subtype metadata through JSON file
2. **Dynamic Rendering**: Load the corresponding keyboard interface according to the subtype ID
3. **Intelligent switching**: Supports manual switching + automatic language detection dual mode
