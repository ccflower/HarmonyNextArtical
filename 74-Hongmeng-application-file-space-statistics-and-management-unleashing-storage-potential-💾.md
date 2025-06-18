# Hongmeng application file space statistics and management: unleashing storage potential 💾

Hongmeng Next provides accurate file space management capabilities to help developers monitor storage usage in real time.This article analyzes core interfaces, statistical methods and optimization strategies~


## 1. Core capabilities: "Digital Dashboard" for space management📊
Hongmeng realizes fine storage management through two major modules:
- **@ohos.file.statvfs**: Get file system-level data (total space/remaining space/number of nodes)
- **@ohos.file.storageStatistics**: Get application-level data (installation package/cache/user data size)


## 2. Key interface quick lookup table🚀
| Functions | Interface/Methods | Return Value Type | Sample Uses |
|---------------------|------------------------------------|------------------|------------------------------|  
| Get application storage statistics | `storageStatistics.getCurrentBundleStats()` | `BundleStats` | Statistics installation package (`appSize`), cache (`cacheSize`) |
| Get file system space | `statvfs.getFreeSize(path)` | `number` (bytes) | Detect the remaining space of `filesDir` |
| Traversal directory size | `File.traverseDir(dirPath, callback)` | - | Recursively calculate all file sizes in the directory |


## 3. Practical combat: Spatial statistics code example 📈
### 1. Application storage details statistics
```typescript  
import { storageStatistics } from '@ohos.file.storageStatistics';  

// Get application installation package, cache, data size
storageStatistics.getCurrentBundleStats((err, stats) => {  
  if (!err) {  
console.log(`install package size: ${stats.appSize / 1024 / 1024} MB`);
console.log(`Cache Size: ${stats.cacheSize / 1024 / 1024} MB`);
console.log(`User data size: ${stats.dataSize / 1024 / 1024} MB`);
  }  
});  
```  

### 2. File system space detection
```typescript  
import { statvfs } from '@ohos.file.statvfs';  
import { getContext } from '@ohos.app.ability';  

const checkStorage = () => {  
  const context = getContext() as common.UIAbilityContext;  
const filesPath = context.filesDir; // Application private file directory
const cachePath = context.cacheDir; // Cache directory

// Detect the total and remaining space of the file system
  statvfs.getTotalSize(filesPath, (err, total) => {  
    statvfs.getFreeSize(filesPath, (err, free) => {  
console.log(`Total file system space: ${total / 1024 / 1024} MB`);
console.log(`Remaining space: ${free / 1024 / 1024} MB`);
    });  
  });  
};  
```  

### 3. Large file scanning (recursively traverse directory)
```typescript  
import { File } from '@ohos.file';  

async function scanLargeFiles(dir: string, threshold = 10 * 1024 * 1024) {  
  const files = await File.list(dir);  
  for (const file of files) {  
    const filePath = `${dir}/${file}`;  
    const stats = await File.stat(filePath);  
    if (stats.isDirectory) {  
await scanLargeFiles(filePath, threshold); // Recursive subdirectory
    } else {  
      if (stats.size > threshold) {  
console.log(`Large file discovery: ${filePath}, size: ${stats.size / 1024 / 1024} MB`);
      }  
    }  
  }  
}  
```  


## 4. Optimization strategy: "combination punch" to free up storage space🎯
### 1. Three principles of cache management
| Strategy | Implementation | Sample Scenario |
|---------------|-------------------------------------------|------------------------------|  
| **Regular Clean** | Set a timer or listen for low memory events to trigger cleaning | Automatically clean up unused cache images every week |
| **Level storage** | Hot data (high frequency) memory, cold data storage disk | Chat emoticon package cache rating |
| **Clean by type**|Preferential deletion of reconstructable temporary files (such as logs/temp pictures) | Clean all files in the `temp/` directory |

### 2. Data compression scheme
```typescript  
// Image compression example (using Tinify library)
import { Tinify } from '@ohos.compression';  

async function compressImage(srcPath, destPath) {  
  const compressedData = await Tinify.compressFile(srcPath, {  
    format: 'webp',  
    quality: 80  
  });  
  await File.writeFile(destPath, compressedData);  
await File.delete(srcPath); // Delete the original image
}  
```  

### 3. Database optimization
```typescript  
// SQLite database compression (clean up idle pages)
import { Database } from '@ohos.sqlite';  

const db = Database.open('app.db');  
db.execSQL('VACUUM;'); // Compress database file
```  


## 5. Monitoring and early warning: "alarm" that stores health 🚨
### 1. Threshold warning mechanism
```typescript  
// Cleanup is triggered when the remaining space is less than 10%
statvfs.getFreeSize(filesPath, (err, free) => {  
  const threshold = total * 0.1;  
  if (free < threshold) {  
cleanCache(); // Call the cleaning function
showToast('Insufficient storage space, cache has been automatically cleaned');
  }  
});  
```  

### 2. User Boot Policy
- Show storage usage details on the settings page
- Provide the "One-click cleaning" button to trigger `File.deleteAllCache()`
- Display of large file lists and support manual deletion


## Summary: "Three-step method" of space management
1. **Statistical Analysis**: Use `storageStatistics` to locate large users
2. **Strategic optimization**: Optimize by "cache grading + data compression + database slimming" combination
3. **Dynamic monitoring**: Set threshold warning, automatically/manually free space
