# Hongmeng File Access Framework: User file access and permission control📂

Hongmeng Next's File Access Framework (File Access Framework, FAF) provides applications with secure and controllable user file access capabilities.This article analyzes its core functions, permission configuration and practical usage~


## 1. FAF core: a safe and convenient file access bridge 🔗
FAF is a user file management framework provided by Hongmeng. It is implemented based on ExtensionAbility and has three major features:
- **Security Isolation**: Limit file access scope through permission control
- **Unified Interface**: Standardized file selection/save/management process
- **Span-device support**: Compatible with multi-terminal file systems (mobile phone/tablet/car machine)


## 2. Permission configuration: "key" to access user files🔑
### 1. Declare file operation permissions (module.json5)
```json  
{  
  "abilities": [  
    {  
      "skills": [  
        {  
"actions": ["ohos.arkui.intent.action.CHOOSE"], // File selection
          "uris": [{ "scheme": "file", "host": "*", "path": "/storage/*" }]  
        },  
        {  
"actions": ["ohos.arkui.intent.action.SAVE"], // File saving
          "uris": [{ "scheme": "file", "host": "*", "path": "/storage/*" }]  
        }  
      ]  
    }  
  ]  
}  
```  

### 2. Runtime authorization mechanism
- When the user triggers file selection/save, the system pops up the authorization pop-up window
- Applications can only access the file path authorized by the user, prohibiting over-authorization operations


## 3. Core interface and practical examples🚀
### 1. File selection (taking picture as an example)
```typescript  
import { fileAccess, wantAgent } from '@ohos.fileAccess';  

// Call the system file selector
const chooseFile = () => {  
  const intent = {  
    action: 'ohos.arkui.intent.action.CHOOSE',  
type: 'image/*', // Limited selection of image types
    entities: ['image/*']  
  };  
  const want = wantAgent.createWant(intent);  
  this.context.startAbility(want, (result) => {  
    if (result && want.response.result) {  
      const fileUri = want.response.result;  
// Get file information (such as size/type)
      fileAccess.getFileInfo(fileUri, (err, info) => {  
console.log('Select image:', info.fileName);
      });  
    }  
  });  
};  
```  

### 2. File saving (text file example)
```typescript  
const saveTextFile = () => {  
  const saveIntent = {  
    action: 'ohos.arkui.intent.action.SAVE',  
type: 'text/plain', // Specify the save type as plain text
extra: { fileName: 'note.txt' } // Preset file name
  };  
  const saveWant = wantAgent.createWant(saveIntent);  
  this.context.startAbility(saveWant, (result) => {  
    if (result) {  
      const saveUri = want.response.result;  
// Write file contents
fileAccess.saveFile(saveUri, 'Hongmeng Development Notes', (err) => {
if (!err) console.log('Save successfully');
      });  
    }  
  });  
};  
```  

### 3. File management operations
```typescript  
// Create a directory
fileAccess.createDir('/storage/emulated/0/app/newFolder', (err) => {  
if (!err) console.log('Directory creation successfully');
});  

// Delete the file
fileAccess.deleteFile('/storage/emulated/0/app/temp.log', (err) => {  
if (!err) console.log('File deletion successfully');
});  
```  


## 4. Security design: the "protective wall" of user data 🛡️
### 1. Access restrictions outside the sandbox
- By default, the application can only access its own sandbox files (`/data/el2/files/`)
- Access to external storage (such as album/download directory) must pass through the FAF unified interface, and direct path operation is prohibited

### 2. Principle of Minimization
- Each file operation requires separate authorization (if you can only access the file after selecting the picture)
- Authorization is only valid for this operation and no permanent access is granted

### 3. Cross-device security policy
- In distributed scenarios, file access needs to be authenticated by device
- Sensitive files (such as privacy images) are prohibited from being transferred across devices


## Summary: 3 steps to achieve secure file access
1. **Declare permissions**: Declare operation permissions such as `CHOOSE`/`SAVE` in the configuration file
2. **Calling interface**: Initiate file operations through the unified interface provided by FAF
3. **Compliance processing**: Follow the process of "User Authorization → Limited Access → Timely Release"
