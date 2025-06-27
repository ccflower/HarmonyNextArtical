# 🗝️ Huawei Hongmeng Context In-depth Analysis: The Practical Guide to the Application of "Master Key"

> As a developer who once caused memory leaks due to improper use of Context, today we will reveal the HarmonyOS Context!The first time I misuse Context, the APP crashed, and now I will make the correct usage of this "app master key" public~


## 1. Context: The "universal ID card" used

### 1. What is Context?

Imagine you entering the mall:
- Context is your "Mall Pass", with it:
✅ Enter the store (start component)
✅ Get map (get resources)
✅ Use elevator (system adjustment service)

HarmonyOS的Context核心能力：  
- **Resource access**: Get strings, pictures and other resources
- **Component Start**: Open a new page, adjust the background service
- **System interaction**: Use sensors, store data, etc.


### 2. Two types: "different permissions" of passes

| Type | Permission Range | Analog |
|----------------|-------------------------|-----------------------|  
| AbilityContext | Permissions for a single Ability | Store-only pass |
| ApplicationContext | All application permissions | Shopping Mall Universal Pass |


## 2. Obtain Context: Find the right "certificate issuing authority"

### 1. Get it in Ability (most commonly used)

```typescript
import { UIAbility } from '@ohos.app.ability';

export default class MainAbility extends UIAbility {
  onStart() {
// Method 1: Get it directly
    const context1 = this.context;
    
// Method 2: By applying the context
    const context2 = this.getApplicationContext();
    
    console.log(`AbilityContext: ${context1}`);
    console.log(`ApplicationContext: ${context2}`);
  }
}
```  

### 2. Get it in AbilitySlice

```typescript
import { AbilitySlice } from '@ohos.app.ability';

export class MainSlice extends AbilitySlice {
  onPageShow() {
// Get Context from the Ability
    const context = this.ability.context;
console.log(Context obtained in `Slice: ${context}`);
  }
}
```  

### 3. Get it in custom components

```typescript
import { getContext } from '@ohos.app.ability';

// Get it from the custom component
const context = getContext(this) as typeof import('@ohos.app.ability').Context;
```  


## 3. File operation: Use Context to open the "file drawer"

### 1. Common file path acquisition

| Method | Purpose | Sample Code |
|-------------------|-----------------------|-----------------------------------|  
| getFilesDir() | Application Data Directory | const filesDir = context.getFilesDir(); |
| getCacheDir() | CacheDir (will be cleaned) | const cacheDir = context.getCacheDir(); |
| getEncryptedFilesDir() | Encrypted directory (storage sensitive data) | const encryptedDir = context.getEncryptedFilesDir(); |

### 2. Practical combat: Save user data to the encrypted directory

```typescript
// Check whether the device is encrypted
if (context.isDeviceEncrypted()) {
// Get the encrypted directory
  const encryptedDir = context.getEncryptedFilesDir().path;
  
// Store sensitive data (such as payment information)
  const filePath = `${encryptedDir}/payment_info.json`;
  fs.writeFileSync(filePath, JSON.stringify(paymentData));
}
```  


## 4. Cross-component communication: Context's "Walkjet" function

### 1. EventHub: Broadcasting between components

```typescript
// Subscribe to events (in the target component)
context.eventHub.on('newMessage', (data) => {
console.log(`Message received: ${data}`);
  this.updateUI(data);
});

// Send event (at the sender)
context.eventHub.publish('newMessage', 'Hello from Context!');
```  

### 2. AppStorage: All apps share data

```typescript
// Store data (such as user information)
context.appStorage.put('userInfo', { name: 'HarmonyUser', level: 'VIP' });

// Get data (in other components)
const userInfo = context.appStorage.get('userInfo');
console.log(`user information: ${userInfo.name}`);
```  

### 3. LocalStorage: component-specific data

```typescript
// Store data (only available to the current component)
context.localStorage.put('componentState', { isLoading: false });

// Get data
const state = context.localStorage.get('componentState');
if (!state.isLoading) {
  this.loadData();
}
```  


## 5. Practical pit avoidance: "Using specifications" of passes

### 1. Memory leak risk (the easiest pit)

```typescript
// Error demonstration: Long life cycle object holds short life cycle Context
let badContext: Context;

function wrongWay(abilityContext: AbilityContext) {
badContext = abilityContext; // Dangerous!Context leaks after Ability is destroyed
}

// Correct way: Use ApplicationContext
function rightWay(context: Context) {
// Use application-level Context for longer life cycle
  const appContext = context.getApplicationContext();
}
```  

### 2. Choose the right Context type in different scenarios

| Scenario | Correct Context Type | Error Type | Consequence |
|--------------|-----------------------|-----------------------|-----------------------|  
| Start a new Ability | AbilityContext | ApplicationContext | Some features are not available |
| Global Data Storage | ApplicationContext | AbilityContext | Data Lost after Component Destruction |


## 6. Performance optimization: "Efficient use method" of the pass

### 1. Cache Context references

```typescript
export class AppData {
  private static _context: Context;

// Save once and use it everywhere
  static init(context: Context) {
    this._context = context.getApplicationContext();
  }

  static get context() {
    return this._context;
  }
}

// Initialization
AppData.init(context);

// use
AppData.context.getFilesDir();
```  

### 2. Avoid frequent acquisitions

```typescript
// Bad writing method (take it every time)
for (let i = 0; i < 100; i++) {
  const dir = this.context.getFilesDir();
// operate...
}

// Optimized writing method (take it once first)
const dir = this.context.getFilesDir();
for (let i = 0; i < 100; i++) {
// Use dir variable
}
```  


## 7. Interview FAQ: Context’s “soul-totoring”

### 1. What is the difference between AbilityContext and ApplicationContext?

- **Life Cycle**: AbilityContext is destroyed with Ability, ApplicationContext is destroyed with application exit
- **Purpose**: The former is used to start the component, the latter is used to global data storage

### 2. Why can't you start an Activity with Application's Context in Activity?

- Report `android.util.AndroidRuntimeException`
- Correct way: Use the Context of Activity or create the FLAG_ACTIVITY_NEW_TASK flag


## The last thought

I remember the first time I used the Context of Activity in a custom view, which caused the View to hold the Context after the Activity was destroyed, and the memory leaked to the APP crashed~ Now I have developed the habit of using ApplicationContext to store global data, and I am no longer afraid of leaking~
