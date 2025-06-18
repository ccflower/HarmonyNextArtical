
# Hongmeng Core File Kit: Minimalist File Management Guide 📁

Hongmeng Next's Core File Kit uses "sandbox isolation + minimalist interface" to achieve safe and efficient file management.This article uses streamlined language to analyze core functions and practical points~


## 1. Core capabilities: "Swiss Army Knife" for file operation🔪
Core File Kit is a lightweight file management tool provided by Hongmeng, focusing on **in-app file lifecycle management**, and supports:
- **Basic operations**: Create/read/write/delete files, traverse directories
- **Security mechanism**: Sandbox isolation (independent storage space)
- **Scenario adaptation**: Configure storage, user data, cache management


## 2. Core interface quick lookup table🚀
| Operation Type | Key Interface | Sample Code |
|----------------|-----------------------|-------------------------------------------|  
| **Open/Create file** | `File.open(path, mode)` | `const fd = File.open('/data/file.txt', File.MODE_CREATE);` |
| **Write data** | `File.write(fd, data)` | `File.write(fd, 'Hello HarmonyOS!');` |
| **Read data** | `File.read(fd, buffer)` | `const buffer = new ArrayBuffer(1024); File.read(fd, buffer);` |
| **Delete File** | `File.delete(path)` | `File.delete('/data/file.txt');` |
| **Check the file exists** | `File.exists(path)` | `if (File.exists('/data/cache')) { ... }` |


## 3. Practical combat: Example of the entire process of file operation📝
```typescript  
import { File } from '@kit.CoreFileKit';  

const FILE_PATH = '/data/storage/el2/files/config.txt';  

async function fileOpsDemo() {  
  try {  
// 1. Open the file (create if it does not exist, read and write mode)
    const fd = File.open(FILE_PATH, File.MODE_READ_WRITE | File.MODE_CREATE);  

// 2. Write configuration data
    const configData = JSON.stringify({ theme: 'dark', fontSize: 16 });  
    File.write(fd, configData);  
console.log('Configuration write successful');

// 3. Read data and parse
    const buffer = new Uint8Array(1024);  
    const bytesRead = File.read(fd, buffer);  
    const content = new TextDecoder().decode(buffer.subarray(0, bytesRead));  
    const config = JSON.parse(content);  
console.log('Current configuration:', config);

// 4. Clean cached files
    File.delete('/data/cache/temp.log');  
console.log('Cache Cleanup Completed');

  } catch (error) {  
console.error('File operation failed:', error);
  }  
}  
```  


## 4. Sandbox isolation: the "moat" of data security 🏰
### 1. Storage structure
```  
Application Sandbox Directory (Example)
├─ files/ # Permanent storage (user data, uninstalled and deleted with the application)
│  └─ config.txt  
├─ cache/ # Temporary cache (the system can be automatically cleaned)
│  └─ temp.jpg  
└─ databases/ # database files (managed by ArkUI X)
```  

### 2. Safety features
- **Isolation**: Other applications cannot access the sandbox path of this application
- **Permission Control**: Only the application itself can read and write files in the sandbox
- **Automatic cleaning**: `cache/` directory file can be automatically deleted by the system according to memory conditions

### 3. Best Practices
```typescript  
// Recommended: Use the system-provided sandbox path API
import { FileManager } from '@ohos.file.fileManager';  

// Get the application private directory (automatically process sandbox path)
const appFilesDir = FileManager.getAppFilesDir();  
const safePath = `${appFilesDir}/user_data.json`;  
```  


## 5. Performance optimization: Make the operation more "lightweight" ⚡
### 1. Streaming reading and writing (large file scenario)
```typescript  
// Read large files in chunks (avoid memory overflow)
const CHUNK_SIZE = 4096;  
const fd = File.open('/data/video.mp4', File.MODE_READ);  
const stream = new FileStream(fd, CHUNK_SIZE);  

while (await stream.readChunk(buffer)) {  
processChunk(buffer); // Process chunked data
}  
```  

### 2. Cache Policy
```typescript  
// Automatically clean cached files from 7 days ago
function cleanOldCache() {  
  const cacheDir = FileManager.getAppCacheDir();  
  const files = File.list(cacheDir);  
  files.forEach(file => {  
    const mtime = File.stat(file).mtime;  
    if (Date.now() - mtime > 7 * 24 * 3600 * 1000) {  
      File.delete(file);  
    }  
  });  
}  
```  


## Summary: 3 steps to master the core usage
1. **Path Management**: Use `FileManager.getAppFilesDir()` to obtain a secure path
2. **Operation process**: open → write/read → close (or delete)
3. **Safety principle**: Sensitive data is stored in `files/`, and temporary data is `cache/`
