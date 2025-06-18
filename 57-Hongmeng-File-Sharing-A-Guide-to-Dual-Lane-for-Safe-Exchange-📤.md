
# Hongmeng File Sharing: A Guide to "Dual Lane" for Safe Exchange 📤

Hi~ I am Xiao L!In Hongmeng development, file sharing is like a "digital highway"—URI and FD have their own advantages.Today, we will master safe and efficient sharing skills to make data exchange between applications smoother~


## 1. Two ways to share: URI vs FD🚗
### Core Difference Comparison Table
| **Dimension** | **URI Sharing** | **FD Sharing** |
|----------------|---------------------------|---------------------------|  
| **Operation difficulty** | Simple (system automatic management permissions) | Complex (need to manually manage file handles) |
| **Sharing Unit** | Single File | Single File or Directory |
| **Permission time limit** | Temporary authorization (the recipient exits and invalidates) | Permanent authorization (the FD is invalidated after closing) |
| **Typical Scenarios** | Temporary Sharing of Text and Pictures | Batch Transfer of Large Files and Directories |

### Applicable scenario selection
- **Select URI**: Send pictures on chat apps and share email attachments
- **Select FD**: File manager transfer folders, audio and video applications batch import


## 2. URI sharing: "click and go" lightweight solution✨
### 1. Sharing side implementation (taking pictures as an example)
```typescript  
import { fileUri, wantConstant } from '@ohos.fileUri';  
import { UIAbility, Want } from '@ohos.app.ability';  

export default class ShareAbility extends UIAbility {  
  onWindowStageCreate() {  
// 1. Get the file URI
    const imagePath = `${this.context.filesDir}/poster.jpg`;  
    const uri = fileUri.getUriFromPath(imagePath);  
    
// 2. Create sharing intent (grant read and write permissions)
    const want: Want = {  
      action: wantConstant.Action.SEND_DATA,  
      uri: uri,  
      type: 'image/jpeg',  
      flags: wantConstant.Flag.AUTH_READ_URI_PERMISSION |  
             wantConstant.Flag.AUTH_WRITE_URI_PERMISSION  
    };  
    
// 3. Initiate sharing
    this.context.startAbility(want).then(() => {  
console.log('Image sharing successfully');
    });  
  }  
}  
```  

### 2. Receive side processing (save file to sandbox)
```typescript  
export default class ReceiveAbility extends UIAbility {  
  onNewWant(want: Want) {  
    if (want.uri) {  
      const sourceUri = want.uri;  
      const destPath = `${this.context.filesDir}/received_${Date.now()}.jpg`;  
      
// Read the URI file and write to the local
      fileIo.copySync(sourceUri, destPath);  
console.log('File saved to:', destPath);
    }  
  }  
}  
```  

### 3. Permission configuration (module.json5)
```json  
{  
  "abilities": [  
    {  
      "name": ".ShareAbility",  
      "skills": [  
        {  
          "actions": ["ohos.arkui.intent.action.SEND_DATA"],  
          "uris": [  
            {  
              "scheme": "file",  
              "host": "*",  
"path": "/data/storage/el1/*" // Allows shared path range
            }  
          ]  
        }  
      ]  
    }  
  ]  
}  
```  


## 3. FD sharing: "Exclusive for large files" efficient channel🚚
### 1. Sharing directory examples (such as document folders)
```typescript  
import { fileIO as fs } from '@ohos.fileIO';  
import { FileDescriptor } from '@ohos.fs';  

function shareDirectory(sourceDir: string) {  
// 1. Open the directory to get FD
  const fd: FileDescriptor = fs.openSync(sourceDir, fs.OpenMode.READ);  
  
// 2. Create FD sharing data
  const shareData = {  
    fd: fd,  
    type: 'directory',  
    metadata: {  
      name: 'ProjectFiles',  
      size: fs.statSync(sourceDir).size  
    }  
  };  
  
// 3. Pass FD through custom channels (such as IPC)
  ipc.send('SHARE_FD', shareData);  
}  
```  

### 2. The receiver handles FD (reads the directory content)
```typescript  
function handleReceivedFD(fd: FileDescriptor) {  
// 1. Read all files in the directory
  const entries = fs.readdirSync(fd);  
  
// 2. Traversing the file
  entries.forEach((entry) => {  
    if (entry.isFile()) {  
      const fileFd = fs.openSync(`${fd.path}/${entry.name}`, fs.OpenMode.READ);  
      const content = fs.readFileSync(fileFd, 'utf8');  
      fs.closeSync(fileFd);  
    }  
  });  
  
// 3. Close FD to release resources
  fs.closeSync(fd);  
}  
```  

### 3. Safety precautions
- **FD life cycle**: The receiver must complete the operation before the FD is closed, otherwise the file will be inaccessible
- **Permission Control**: Only authorized directories are allowed to be shared (such as `files/` directory in the application sandbox)


## 4. Safety control: "Traffic rules" for data exchange 🛑
### 1. Principle of Minimization
- Grant only the necessary permissions:
  ```typescript  
// Only grant read permissions (avoid the receiver tampering with files)
  flags: wantConstant.Flag.AUTH_READ_URI_PERMISSION  
  ```  

### 2. Sensitive data encryption
```typescript  
// Encrypt files before sharing
import { crypto } from '@ohos.security';  

async function encryptAndShare(filePath: string) {  
  const key = crypto.generateKey('AES', 256);  
  const encryptedData = await crypto.encryptFile(filePath, key);  
  const uri = fileUri.getUriFromPath(encryptedData.path);  
// Share the encrypted URI
  shareUri(uri);  
}  
```  

### 3. Temporary permission management
- URI sharing automatic recycling permission: After the receiver's application exits, the URI will automatically expire
- FD sharing needs to be closed manually:
  ```typescript  
// Close immediately after the receiver has used the FD
  fs.closeSync(fd);  
  ```  


## 5. Practical scenario: Cross-device batch file transfer 📱→💻
### Scene Description
Users quickly share folders in their phones to their tablets through Hongmeng's "One Touch Pass":
1. **Mobile terminal (sharing party)**:
- Select the target folder and generate FD
- Transfer of FD and metadata via distributed soft bus

2. **Plate end (receiver)**:
- Receive FD, verify directory permissions
- traversal FD reading file and save it to local

### Core Code (simplified version)
```typescript  
// Mobile: Send FD via soft bus
import { distributedBus } from '@ohos.distributedBus';  

const bus = distributedBus.create('file_share_channel');  
bus.on('receive_fd', (fdData) => {  
// Post-processing of FD on the tablet
  handleReceivedFD(fdData.fd);  
});  

// Tablet: Receive and process FD
function handleReceivedFD(fd: FileDescriptor) {  
// Check whether it is a allowed share directory
  if (!isAuthorized(fd.path)) {  
    fs.closeSync(fd);  
throw new Error('illegal directory');
  }  
// Execute file copy logic
}  
```  


## 6. Guide to avoid pits⚠️
### 1. URI sharing FAQ
- **Insufficient permissions**: The SEND_DATA action was not declared in `module.json5`, resulting in sharing failure
- **File path error**: Use absolute path instead of sandbox path, for example `/sdcard/` may not have permission to access

### 2. Things to note when sharing FD
- **FD泄漏**：忘记关闭FD会导致文件句柄耗尽，需在`finally`块中关闭  
  ```typescript  
  try {  
    const fd = fs.openSync(...);  
// Use FD
  } finally {  
    fs.closeSync(fd);  
  }  
  ```  
- **Cross-process issues**: FD is only valid in the current process, cross-process delivery needs to be copied through `dup()`

### 3. Test points
- Simulate low permission scenarios: Verify that unauthorized files are inaccessible
- Stress test: share multiple large files at the same time and observe system resource usage


## Summary: Sharing the "Three Choice Rule"
1. **Lightweight temporary**: Choose URI (such as social sharing, single file transfer)
2. **Massive persistence**: Choose FD (such as file management, directory migration)
3. **Sensitive data**: Must encrypt + temporary permissions (prevent data leakage)
