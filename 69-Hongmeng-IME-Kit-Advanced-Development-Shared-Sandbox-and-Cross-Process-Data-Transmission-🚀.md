
# Hongmeng IME Kit Advanced Development: Shared Sandbox and Cross-Process Data Transmission 🚀

The shared sandbox mechanism of Hongmeng input method makes cross-process data sharing safe and efficient.This article analyzes sandbox configuration and core data transmission logic, and attaches practical code to help you realize advanced functions such as cloud synchronization~


## 1. Shared sandbox: a "safety safe" for cross-process communication 📦
### Core Concept
- **Sandbox Isolation**: Each application has an independent sandbox by default (`/data/el2/user/0/package/`). Shared sandboxes are accessed across processes through `data-group`
- **Permission Control**: Only processes within the same `data-group` can be read and written, and must be declared in `module.json5`
- **Data encryption**: Supports AES-256 encrypted storage to prevent data leakage

### Application Scenario
| Scenario | Implementation method |
|--------------------|-------------------------------------------|  
| Input method → ​​Main application synchronizes theme | Sandbox stores theme configuration files, and the main application updates the UI after reading |
| Cross-device vocabulary synchronization | Sandbox data is synchronized across devices through HMS Core cloud service |
| Extended function data interaction | Voice input module recognition results are written to the sandbox, and input method is read |


## 2. Sandbox configuration: open the "data tunnel" between processes 🔑
### 1. Declare the shared group ID (module.json5)
```json  
{  
  "module": {  
    "extensionAbilities": [  
      {  
        "name": ".InputMethodService",  
"data-group-ids": ["ime_share_group"] // Input method extension group ID
      }  
    ],  
    "abilities": [  
      {  
        "name": ".MainAbility",  
"data-group-ids": ["ime_share_group"] // The main application group ID must be consistent
      }  
    ]  
  }  
}  
```  

### 2. Get the sandbox path
```typescript  
// Get in the input method extension
const shareDir = this.context.getGroupDir('ime_share_group');  
// The paths in the main application are consistent
```  

### 3. Example directory structure
```  
ime_share_group/  
├─ config/ # configuration file (theme, thesaurus)
│  ├─ theme.json  
│  └─ user_dict.txt  
├─ cache/ # Temporary data (voice cache, clipboard)
└─ logs/ # debug log
```  


## 3. Data transmission: file operation and serialization skills📄
### 1. Write data (example: save user vocabulary)
```typescript  
import fs from '@ohos.file.fs';  

async function saveUserDictionary(words: string[]) {  
  const filePath = `${shareDir}/config/user_dict.txt`;  
  const content = words.join('\n');  
  await fs.writeFile(filePath, content, { encoding: 'utf8' });  
}  
```  

### 2. Read data (Example: Loading topic configuration)
```typescript  
async function loadThemeConfig() {  
  const filePath = `${shareDir}/config/theme.json`;  
  try {  
    const content = await fs.readFile(filePath, { encoding: 'utf8' });  
    return JSON.parse(content) as ThemeConfig;  
  } catch (error) {  
return defaultTheme; //Return to default configuration if read failed
  }  
}  
```  

### 3. Binary data processing (example: cache voice files)
```typescript  
async function cacheVoiceData(voiceBuffer: ArrayBuffer) {  
  const filePath = `${shareDir}/cache/voice_${Date.now()}.bin`;  
  const fd = await fs.open(filePath, fs.OpenMode.CREATE | fs.OpenMode.WRITE);  
  await fs.writeSync(fd, new Uint8Array(voiceBuffer));  
  await fs.close(fd);  
}  
```  


## 4. Security Strengthening: Prevent data abuse 🛡️
### 1. Permission verification
```typescript  
// Verify the caller's identity when data is read
function checkCallerPermission() {  
  const callerBundle = AbilityFeatureManager.getCurrentAbilityInfo().bundleName;  
  if (callerBundle !== 'com.example.ime.main') {  
throw new Error('No permission to access the shared sandbox');
  }  
}  
```  

### 2. Encrypted storage
```typescript  
import { crypto } from '@ohos.security';  

// Encryption during writing
async function encryptAndSave(data: string, key: string) {  
  const cipherText = await crypto.encrypt(data, key, 'AES/CBC/PKCS7Padding');  
  await fs.writeFile(`${shareDir}/secure_data.dat`, cipherText);  
}  

// Decryption during reading
async function decryptAndLoad(key: string) {  
  const cipherText = await fs.readFile(`${shareDir}/secure_data.dat`);  
  return crypto.decrypt(cipherText, key, 'AES/CBC/PKCS7Padding');  
}  
```  

### 3. Data Cleaning Strategy
```typescript  
// Clean out expired cache regularly (such as voice files 7 days ago)
async function cleanOldCache() {  
  const files = await fs.readdir(`${shareDir}/cache`);  
  files.forEach(file => {  
    const mtime = fs.statSync(`${shareDir}/cache/${file}`).mtime;  
    if (Date.now() - mtime > 7 * 24 * 3600 * 1000) {  
      fs.unlinkSync(`${shareDir}/cache/${file}`);  
    }  
  });  
}  
```  


## 5. Practical scenario: Cross-process theme synchronization💅
### 1. Main application setting theme
```typescript  
// Save theme configuration in the main application
async function setTheme(theme: Theme) {  
  const config = { themeColor: theme.color, fontSize: theme.size };  
  await saveUserConfig('theme.json', config);  
// Notify input method to update topic
  this.context.startAbility({  
    bundleName: 'com.example.ime',  
    abilityName: 'InputMethodService',  
    action: 'update_theme'  
  });  
}  
```  

### 2. Input method to monitor topic changes
```typescript  
// Receive topic update messages in the input method service
onRequest(want: Want) {  
  if (want.action === 'update_theme') {  
    this.loadThemeConfig().then(theme => {  
this.updateKeyboardStyle(theme); // Re-render the keyboard style
    });  
  }  
}  
```  

### 3. Theme style update
```typescript  
function updateKeyboardStyle(theme: ThemeConfig) {  
  this.panel.setUiContent(() => {  
    Column() {  
Grid() { /* Use theme.color as background color */ }
    }.backgroundColor(theme.themeColor);  
  });  
}  
```  


## 6. Performance optimization: Make data interaction more efficient
### 1. Memory Mapping (MMAP)
```typescript  
// Use memory mapping to improve speed when reading large files
const fd = await fs.open(filePath, fs.OpenMode.READ);  
const mmap = await fs.mmap(fd, 0, fs.MMAP_ACCESS_MODE.MMAP_ACCESS_READ);  
const content = new TextDecoder().decode(mmap);  
fs.munmap(mmap);  
```  

### 2. Incremental update
```typescript  
// Only the difference is written when the lexicon is updated
async function updateDictionaryDiff(newWords: string[]) {  
  const oldWords = await loadUserDictionary();  
  const diff = newWords.filter(word => !oldWords.includes(word));  
  await fs.appendFile(`${shareDir}/config/user_dict.txt`, diff.join('\n'));  
}  
```  


## Summary: The "three elements" of shared sandbox development
1. **Configuration first**: Ensure that the main application is consistent with the `data-group` extended by the input method
2. **Security first**: Sensitive data encrypted storage, strictly verify access permissions
3. **Efficiency priority**: Use MMAP for large files, and update incremental data differentials
