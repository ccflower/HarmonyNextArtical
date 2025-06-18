
# Hongmeng Want: The "universal messenger" guide for component communication📩

hello!I am Xiao L, the female programmer who "plays component communication" in Hongmeng application~ Do you know?The various components of Hongmeng application (such as Ability and Service) are like "independent islands", and **Want** is the "magic bridge" connecting them!Let’s talk about this universal messenger that can convey “arbitrary information” today, and see how it makes the components collaborative seamless~


## 1. What is Want?"Express package" for component communication🚚
**The essence of Want**:
An object carrying "communication instructions + data" is specifically responsible for passing information between Hongmeng components and supporting cross-device communication.
**Core role**:
- Tell the target component "Who am I?" and "What am I going to do"
- Carry out "express content" such as parameters, URIs, actions, etc.
- Support explicitly specifying targets or implicitly matching targets

**A typical Want looks like this**:
```typescript
const want: Want = {
deviceId: '', // Target device (empty local)
bundleName: 'com.example.app', // Target application package name
abilityName: 'DetailAbility', // Target component name
uri: 'data://student/123', // Data URI
action: 'ohos.want.action.VIEW', // Operation action
  parameters: { 
    id: 123, 
title: 'Hongmeng Development Guide'
} // Custom parameters
};
```


## 2. Explicit Want vs Implicit Want: Accurate delivery vs citywide calls📢
### (I) Explicit Want: Accurate delivery of "name"
**Core logic**: Directly specify the "full address" (package name + component name) of the target component, just like writing the recipient address in a courier order.
**Applicable scenarios**:
- Definite target components known (like Ability jumps within an application)
- Scenarios where jump logic is required

**Code Example**:
```typescript
// Start FuncAbility in the same application
const explicitWant: Want = {
  bundleName: 'com.example.myapp',
  abilityName: 'FuncAbility'
};
Common.startAbility(explicitWant);
```

**Matching Rules**:
1. **Package name required**: It must be exactly the same as the target component's `bundleName`
2. **Component name required**: It must be exactly the same as the target component's `abilityName`
3. **Parameter optional**: If the target component declares parameter constraints, the parameter type must be matched.


### (II) Implicit Want: Intelligent matching of "city broadcast"
**Core logic**: No specific components are specified, and matching is initiated through features such as "action + type + URI", similar to "city calls to meet the conditions".
**Applicable scenarios**:
- The target component is unclear (such as calling the system browser, map service)
- Scenarios that need to support third-party application extensions

**Code Example**:
```typescript
// Open an HTTP link (the system automatically matches the browser)
const implicitWant: Want = {
  action: 'ohos.want.action.VIEW',
  uri: 'https://harmonyos.com',
  entities: ['entity.system.browsable']
};
Common.startAbility(implicitWant);
```

**Matching Rules**:
1. **Action (action) required**: such as `VIEW` (View), `EDIT` (Edit)
2. **Entities auxiliary**: indicate the purpose of the component (such as `browsable` means browsable)
3. **URI+type (type) filtering**: Match data format (such as `image/jpeg`)
4. **Priority Mechanism**: System Components > Third-party Components, priority matching for high-priority configurations


## 3. Want parameter analysis: "Secret List" in express parcel 📋
### (I) Basic information parameters
| Attribute name | Function description | Requirements | Sample value |
|--------------|------------------------------|--------|----------------------------|
| deviceId | Target device ID (cross-device communication) | Optional | 'device_001' |
| bundleName | Target application package name | Explicitly required | 'com.tmall.client' |
| abilityName | Target component name | Explicitly required | 'MainAbility' |
| moduleName | Target module name | Optional | 'feature_account' |

### (II) Match feature parameters
| Attribute name | Function description | Implicit required | Sample value |
|--------------|------------------------------|---------|----------------------------|
| action | Action (semantic identifier) ​​| Yes | 'ohos.want.action.SHARE' |
| entities | Component Entity (Function Tag) | Yes | ['entity.system.shareable']|
| uri | Data URI (Resource Locator) | Optional | 'file:///storage/pic.jpg' |
| type | MIME type (data format) | optional | 'video/mp4' |

### (III) Business data parameters
| Attribute name | Function description | Requirements | Sample value |
|--------------|------------------------------|--------|----------------------------|
| parameters | Custom parameters (key value pairs) | Optional | { userId: 123, isVip: true }|


## 4. Practical scenario: Jump from the page to the underlying service📱
### (I) Scenario 1: In-app page jump (explicit Want)
**Requirements**: Jump from home page Ability to details page Ability and pass the product ID
```typescript
// Home page code
const want: Want = {
bundleName: getCurrentAbility().bundleName, // Current application package name
abilityName: 'DetailAbility', // Detail page component name
parameters: { productId: 'P001' } // Pass the product ID
};
Common.startAbility(want);

// Details page receive parameters
const productId = this.want.parameters?.productId; // Take out the parameters
```

### (II) Scenario 2: Calling system service (implicit Want)
**Requirements**: Call the system camera to take photos
```typescript
const cameraWant: Want = {
action: 'ohos.want.action.CAPTURE', // Photo action
uri: 'file:///dcim/camera.jpg', // Photo storage URI
type: 'image/jpeg', // Image type
entities: ['entity.system.camera'] // Camera physical
};
Common.startAbility(cameraWant);
```

### (III) Scenario 3: Cross-device communication (explicit Want)
**Requirements**: Mobile application launches file transfer service on tablet
```typescript
const remoteWant: Want = {
deviceId: 'tablet_001', // Tablet device ID
bundleName: 'com.example.filetransfer', // Service Pack Name
abilityName: 'TransferService', // Service component name
parameters: { filePath: '/data/file.zip' } // Transfer file path
};
Common.startAbility(remoteWant);
```


## 5. Guide to avoid pits: "Five Don'ts" used by Want ⚠️
### (I) Don't omit action in implicit Want
```typescript
// ❌ Error: Missing action cannot match
const invalidWant = { uri: 'https://test.com' }; 

// ✅ Correct: Must include actions and entities
const validWant = { 
  action: 'ohos.want.action.VIEW', 
  uri: 'https://test.com', 
  entities: ['entity.system.browsable'] 
};
```

### (II) Don't confuse explicit/implicit required parameters
| Type | Required Parameters | Common Errors |
|----------|------------------------------|------------------------------|
| Explicit Want | bundleName, abilityName | missed bundleName |
| Implicit Want | action, entities | Missing writing entities causes matching failure |

### (III) Don't pass large files in parameters
**Reason**: Want's parameter passing is based on the IPC mechanism, and large files will cause performance degradation or even crashes.
**Solution**:
- Pass file URI instead (such as storage path, network address)
- Pass binary data using shared memory or file system

### (IV) Don't ignore cross-device permissions
**Scenario**: When starting components of other devices, you need to declare permissions in config.json
```json
{
  "reqPermissions": [
    {
      "name": "ohos.permission.DISTRIBUTED_DATASYNC"
    }
  ]
}
```

### (V) Do not start Want in the background thread
**Error**: Calling `startAbility` directly on the child thread may cause UI thread to block
**Correct**: Execute through the UI thread scheduler
```typescript
// ✅ Use UI thread scheduling
import { EventHandler } from '@ohos.app.ability';
const handler = new EventHandler();
handler.postTask(() => {
  Common.startAbility(want);
});
```


## 6. Future trend: Smarter Want matching system🤖
### (I) "Intention Chain" mechanism
Support combining multiple Wants to form "intention chains", for example:
1. Call the camera to take a photo first (the first Want)
2. Automatically pass photo URI to image editing component (second Want)

### (II) "Context-aware" matching
Automatically optimize Want matching based on user scenarios, for example:
- When users are outdoors, priority match the offline map component
- Automatically adjust the Want parameters of the audio playback component when the headset is detected

### (III) "Low Code Visualization" configuration
In the future, Want configuration panel may be provided in HarmonyOS Studio, which supports the rapid generation of Want parameters through drag and drop, lowering the development threshold~


## Summary: Want's "universal formula" 📚
**Component communication efficiency = (correctly select explicit/implicit × parameter accuracy) ÷ Time-consuming to match**
- Clear the goal → Use explicit Want (fast, accurate and ruthless)
- Fuzzy Requirements → Use implicit Want (flexible matching)
- Cross-device scenario → Must pass deviceId+check permissions
