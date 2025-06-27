# 🔧 HarmonyOS system features are convenient to access: seamless calls from camera to settings

## 1. System Picker component: "Quick Channel" for resource selection

### 1. Camera and Scan Code (CameraPicker)
- **Function**: Take photos, record video, scan code, and identify documents
- **Core parameters**:
- `type`: PICTURE (photo taken)/VIDEO (video recording)/BARCODE (scan code)
- `quality`: picture quality (HIGH/MEDIUM/LOW)

```typescript
import { CameraPicker } from '@ohos.arkui.ability.camera';

const cameraPicker = new CameraPicker();
cameraPicker.startCamera({
  type: CameraPickerType.PICTURE,
  quality: CameraQuality.HIGH,
  onResult: (data) => {
const imageUri = data.uri; // Photo result URI
  }
});
```  

### 2. File Management (DocumentViewPicker)
- **Function**: Select documents, audio, video and other files
- **Core parameters**:
- `type`: ALL (all)/DOCUMENT (document)/AUDIO (audio)

```typescript
import { DocumentViewPicker } from '@ohos.arkui.ability.filemanager';

const documentPicker = new DocumentViewPicker();
documentPicker.startDocumentView({
  type: DocumentViewPickerType.DOCUMENT,
  onResult: (data) => {
const fileUri = data.uri; // Selected file URI
  }
});
```  

### 3. ContactsPicker
- **Function**: Select contact information
- **Core parameters**:
- `type`: ALL (all)/PHONE (phone)/EMAIL (email)

```typescript
import { ContactsPicker } from '@ohos.arkui.ability.contacts';

const contactsPicker = new ContactsPicker();
contactsPicker.startContacts({
  type: ContactsPickerType.ALL,
  onResult: (data) => {
const contactInfo = data; // Contact name, phone number and other information
  }
});
```  


## 2. System function interface: direct core capability

### 1. SettingAbility
- **Jumping method**: Pull up through Action
- **Core parameters**:
  - `action`：'ohos.intent.action.SHOW_SETTINGS'  
- `uri`: Set page identity (such as `appsetting`/`wifi`)

```typescript
import { common } from '@ohos.app.ability.common';

const want = {
  action: 'ohos.intent.action.SHOW_SETTINGS',
uri: 'wifi' // Turn on WiFi settings directly
};
context.startAbility(want);
```  

### 2. Make a phone call (TelephonyKit)
- **Permission**: `ohos.permission.CALL_PHONE` required
- **Core interface**: `makeCall(phoneNumber)`

```typescript
import { Telephony } from '@ohos.arkui.ability.telephony';

const telephony = Telephony.getTelephony();
telephony.makeCall('10086'); // Call customer service
```  

### 3. CalendarAbility
- **Jumping method**: Pull up through Action
- **Parameter extension**: Can carry date parameters to target the specified date

```typescript
import { common } from '@ohos.app.ability.common';

const want = {
  action: 'ohos.intent.action.SHOW_CALENDAR',
parameters: { date: '2023-12-31' } // Open the specified date
};
context.startAbility(want);
```  


## 3. Key points of development

### 1. Permission processing
| Functions | Required Permissions | Description |
|--------------|---------------------------|-----------------------|  
| Camera | `ohos.permission.CAMERA` | Photo/video must be taken |
| Contact | `ohos.permission.READ_CONTACTS` | Read contact information |
| Make a call | `ohos.permission.CALL_PHONE` | Direct dialing required |

### 2. Result callback processing
```typescript
// Unified result processing mode
{
  onResult: (data) => {
    if (data.success) {
      processData(data.result);
    } else {
showToast('Action Cancel');
    }
  },
  onError: (code, msg) => {
console.error(`Error code ${code}: ${msg}`);
  }
}
```  

### 3. System application adaptation
- Different device system application versions may be different, exception handling is required
- Priority is given to the use of system Picker components, compatibility is better than direct jump


## Summarize
HarmonyOS provides the ability to easily access cameras, files, contacts and other system functions through system Picker components and specific interfaces.During development, you need to pay attention to permission application, result callback processing and device compatibility. Use `startAbility` and system components reasonably to create a smooth system function call experience and improve application practicality and user experience.
