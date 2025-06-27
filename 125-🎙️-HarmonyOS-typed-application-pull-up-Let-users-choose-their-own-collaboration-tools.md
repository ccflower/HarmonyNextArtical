# 🎙️ HarmonyOS typed application pull up: Let users choose their own collaboration tools

## 1. Vertical application types and core parameters

### 1. Navigation class application (type `navigation`)
| Parameter name | Type | Required | Description |
|-----------------------|---------|------|-------------------------------|  
| sceneType | number | Yes | 1-Route Planning 2-Navigation 3-Location Search |
| destinationLatitude | number | Yes | End Latitude |
| destinationLongitude | number | Yes | End Longitude |
| vehicleType | number | No | 0-Driving 1-Walk 2-Cycling 3-Bus |

**Code Example**
```typescript
const wantParam = {
  sceneType: 1,
  destinationLatitude: 39.9042,
  destinationLongitude: 116.4074,
  vehicleType: 0
};
context.startAbilityByType("navigation", wantParam, callback);
```  

### 2. Financial applications (type `finance`)
| Parameter name | Type | Required | Description |
|---------------|---------|------|-----------------------|  
| sceneType | number | Yes | 1-Transfer 2-Credit Card Repayment |
| bankCardNo | string | No | Bank Card Number |

**Code Example**
```typescript
const wantParam = {
  sceneType: 1,
  bankCardNo: "6222021100012345678"
};
context.startAbilityByType("finance", wantParam, callback);
```  


## 2. Media and file processing types

### 1. Image editing (type logo `photoEditor`)
```typescript
// Pull up the image editing application and pass the image URI
const wantParam = {
  uri: "file:///sdcard/photo.jpg"
};
context.startAbilityByType("photoEditor", wantParam, (result) => {
const editedUri = result?.uri; // Process the edited image URI
});
```  

### 2. File processing (pull up through Action)
```typescript
// Open the text file
const want = {
  action: "ohos.want.action.viewData",
  uri: "file:///sdcard/doc.txt",
  type: "general.plain-text",
  flags: common.WantConstant.Flags.FLAG_AUTH_READ_URI_PERMISSION
};
context.startAbility(want);
```  


## 3. Mail application (type `mail`)
| Parameter name | Type | Description |
|-----------|-----------|-----------------------|  
| email | string[] | recipient's email |
| subject | string | email subject |
| body | string | email text |
| stream | string[] | Attachment URI List |

**Code Example**
```typescript
const wantParam = {
  email: ["user@example.com"],
subject: "Feedback Email",
body: "Problem description...",
  stream: ["file:///sdcard/attachment.jpg"]
};
context.startAbilityByType("mail", wantParam, callback);
```  


## 4. Core points of development

1. **Type identification specification**
- The system-defined type identifier must be used (such as `navigation`/`finance`)
- Custom types need to declare `skills` in the configuration file

2. **Callback processing**
   ```typescript
   const callback = {
onResult: (data) => { /* Processing returns result */ },
onError: (code, msg) => { /* Error handling */ }
   };
   ```

3. **Permission Control**
- File class operations require the `FLAG_AUTH_READ_URI_PERMISSION` and other permission flags
- It is recommended to add additional permission verification for sensitive operations (such as financial transfers).


## Summarize
Through the `startAbilityByType` interface, HarmonyOS implements typed application pulling, allowing users to independently select applications of the same type (such as map/bank/editor).During development, you need to pay attention to type identification accuracy, parameter integrity and callback processing. Combined with `WantParam`, you can create a flexible application collaboration experience.
