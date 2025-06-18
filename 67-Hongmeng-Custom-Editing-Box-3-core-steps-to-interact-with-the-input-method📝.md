
# Hongmeng Custom Editing Box: 3 core steps to interact with the input method📝

The key to developing a custom editing box in Hongmeng is to achieve in-depth interaction with the input method through `InputMethodController`.This article uses the simplest code to help you master the functions of binding, event monitoring and independent editing~


## 1. Input method binding: establish a "connection" between the editing box and the keyboard 🔗
### 1. Core method: attach()
```typescript  
import { inputMethod } from '@ohos.ime';  

// Get the controller instance
private controller = inputMethod.getController();  

// Bind the input method and display the keyboard
async bindIme() {  
  await this.controller.attach(true, {  
    inputAttribute: {  
textInputType: inputMethod.TextInputType.TEXT, // Input type (text/number, etc.)
enterKeyType: inputMethod.EnterKeyType.DONE // Enter key style
    }  
  });  
this.controller.showKeyboard(); // Actively display the keyboard
}  
```  

### 2. Unbinding and hiding
```typescript  
// Unbind when the focus is lost
async unbindIme() {  
  await this.controller.detach();  
  this.controller.hideKeyboard();  
}  
```  


## 2. Event monitoring: Capture input "every move" 🎚️
### 1. Listen to text insertion
```typescript  
// Binding events during initialization
this.controller.on('insertText', (text) => {  
this.handleTextInsert(text); // Custom text processing logic
});  

// Example: Limit input length to 20 words
private handleTextInsert(text: string) {  
  if (this.inputText.length < 20) {  
    this.inputText += text;  
  } else {  
showToast('Input length has reached the upper limit');
  }  
}  
```  

### 2. Listen to delete operations
```typescript  
this.controller.on('deleteLeft', (length) => {  
this.inputText = this.inputText.slice(0, -length); // Delete the character on the left side of the cursor
});  
```  

### 3. Listen to the Enter key
```typescript  
this.controller.on('keyEvent', (event) => {  
  if (event.keyCode === inputMethod.KeyCode.ENTER) {  
this.onSubmit(); // Trigger the submission logic
  }  
});  
```  


## 3. Independent editing function: create a "smart" input box 🧠
### 1. Cursor Control
```typescript  
// Move the cursor to the specified position
this.controller.setCursorPosition(5); // After moving to the 5th character

// Select text (from 3rd to 8th characters)
this.controller.setSelection(3, 8);  
```  

### 2. Text formatting (example: automatic completion of mailbox input)
```typescript  
this.controller.on('insertText', (text) => {  
  if (!this.inputText.includes('@') && text === '@') {  
this.inputText += 'example.com'; // Automatic complete domain name
this.controller.setCursorPosition(this.inputText.length); // Move the cursor to the end
  }  
});  
```  

### 3. Custom keyboard trigger
```typescript  
// Display the custom keyboard when clicking the edit box
@Entry  
@Component  
struct CustomInput {  
  @State inputText = '';  

  build() {  
    Column() {  
      TextInput(this.inputText)  
        .onClick(() => {  
this.bindIme(); // bind input method and display keyboard
        })  
    }  
  }  
}  
```  


## 4. Practical components: complete custom editing box code📄
```typescript  
import { inputMethod } from '@ohos.ime';  

@Component  
struct CustomEditText {  
  @State text = '';  
  private controller = inputMethod.getController();  

// Lifecycle: Binding events at creation
  aboutToAppear() {  
    this.initIme();  
  }  

  private initIme() {  
    this.controller.on('insertText', (t) => this.text += t);  
    this.controller.on('deleteLeft', () => this.text = this.text.slice(0, -1));  
  }  

  build() {  
    Row() {  
// Customize the edit box style
      Text(this.text)  
        .padding(8)  
        .border({ width: 1, color: '#D0D0D0' })  
        .cornerRadius(4)  
.onClick(() => this.controller.showKeyboard()) // Click to call the keyboard

Button('Clear')
        .margin(8)  
        .onClick(() => {  
          this.text = '';  
this.controller.deleteSurroundingText(this.text.length, 0); // Synchronously delete input method cache
        })  
    }  
  }  
}  
```  


## 5. Optimization skills: Improve the "silky" of interaction✨
### 1. The keyboard automatically adapts to the input type
```typescript  
//Display different keyboards according to input type (such as numeric keyboard)
async bindIme(type: inputMethod.TextInputType) {  
  await this.controller.attach(true, {  
    inputAttribute: { textInputType: type }  
  });  
}  

// Example of usage: Phone number input
this.bindIme(inputMethod.TextInputType.PHONE);  
```  

### 2. Prevent duplicate binding
```typescript  
private isAttached = false;  

async attachSafely() {  
  if (!this.isAttached) {  
    await this.controller.attach(true);  
    this.isAttached = true;  
  }  
}  
```  


## Summary: The "Three Principles" of Interaction
1. **Bin first and then use **: The input method connection must be established through `attach()`
2. **Event-driven**: All input operations are implemented by listening to events
3. **Bidirectional synchronization**: The content of the edit box is consistent with the input method status
