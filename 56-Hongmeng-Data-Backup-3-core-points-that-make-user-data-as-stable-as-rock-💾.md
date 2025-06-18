
# Hongmeng Data Backup: 3 core points that make user data "as stable as rock" 💾

Hi~ I am Xiao L!In Hongmeng development, data backup is like an "electronic safe" - ensuring that user data is not lost during application reinstallation and device migration.Today, I will take you to disassemble the core mechanism of Hongmeng data backup and master 3 key development points!


## 1. Backup framework: "underlying infrastructure" for data security 🏗️
### Core Principle
- **Sandbox Isolation**: Each application has an independent backup directory, and the data is stored in isolation according to the `application package name`
- **Automatic trigger**: The system automatically starts backup/restore during application uninstallation, OTA upgrade, and device migration
- ** Version compatibility**: Supports smooth data migration across versions (such as HarmonyOS 3→Next)

### Backup directory structure
```  
/data/storage/el1/base/.backup/restore/  
├── app/ # Application data (such as user settings, database)
│   └── com.example.app/  
│ ├── data.db # database file
│       └── config.json  
└── user/ # User files (such as pictures, documents)
    └── com.example.app/  
        └── photos/  
            └── 202406_trip.jpg  
```  

### Key Scenarios
| Scene | Trigger Conditions | Core Actions |
|---------------------|---------------------------|---------------------------|  
| Application uninstallation | When users uninstall the application | Automatic backup of sandbox data to recovery directory |
| Equipment OTA upgrade | The system detects a new version to be installed | Back up the data first, and restore it after upgrading |
| Cross-device migration | User enables "Data migration" function | Encrypted transfer of backup data to new devices |


## 2. BackupExtensionAbility: the "brain" of custom backup 🧠
### Core Lifecycle Approach
```typescript  
import { BackupExtensionAbility, BundleVersion } from '@ohos.backup';  

export default class MyBackupAbility extends BackupExtensionAbility {  
// Backup logic (such as compressed data, encrypted storage)
  async onBackup() {  
// 1. Collect a list of files that need to be backed up
    const files = ['data.db', 'settings.json'];  
// 2. Compress and encrypt data
    await compressAndEncrypt(files, 'backup.tar.gz');  
// 3. Save to the system backup directory
    await this.saveToBackupDir('backup.tar.gz');  
  }  

// Recovery logic (such as verification files, data migration)
  async onRestore(targetVersion: BundleVersion) {  
// 1. Check the integrity of the backup file
    const isValid = await verifyBackupFile('backup.tar.gz');  
    if (!isValid) return;  
// 2. Decrypt and decompress data
    await decryptAndExtract('backup.tar.gz', this.appSandboxDir);  
// 3. Handle version compatibility (such as old data format conversion)
    if (targetVersion < '1.2.0') {  
      await migrateLegacyData();  
    }  
  }  
}  
```  

### Version Compatibility Policy
```typescript  
// Execute different recovery logic according to the target version number
onRestore(targetVersion: BundleVersion) {  
  switch (targetVersion.name) {  
    case '1.0.0':  
// Rename the database field when migrating from HarmonyOS 3
      renameDbField('old_field', 'new_field');  
      break;  
    case '2.0.0':  
// Upgrade encryption algorithm when migrating from HarmonyOS Next Beta
      upgradeEncryption();  
      break;  
  }  
}  
```  


## 3. Security and performance optimization: "double guarantee" of backup 🛡️
### 1. Data security design
- **Encrypted Storage**:
  ```typescript  
  import { crypto } from '@ohos.security';  

  async function encryptData(data: Buffer, key: string) {  
    const cipher = await crypto.createCipher('AES-256-CBC', key);  
    return cipher.update(data, 'binary', 'base64');  
  }  
  ```  
- **Permission Control**:
The backup directory can only be read and written by itself by default. Other applications need to apply for `ohos.permission.READ_BACKUP_DATA` permission (requires Huawei for review)

### 2. Performance optimization skills
- **Incremental backup**: Only the changed data is backed up (the file hash value needs to be recorded)
  ```typescript  
// Compare file hashings and only backup modified files
  const changedFiles = getChangedFiles(previousHash, currentHash);  
  await backupFiles(changedFiles);  
  ```  
- **Async processing**: Use `setTimeout` or `requestIdleCallback` in `onBackup` to avoid blocking the main thread
  ```typescript  
  onBackup() {  
    requestIdleCallback(async () => {  
await heavyBackupTask(); // Time-consuming operation is executed in the idle period
    });  
  }  
  ```  

### 3. Common Error Handling
| Error Type | Cause Analysis | Solution |
|--------------------|------------------------------|---------------------------|  
| Backup failed | Insufficient storage space | Prompt the user to clean up the space |
| Data exception after recovery | Backup file corruption | Verify file integrity and provide default data |
| Cross-version compatibility issues | Old version data format incompatible | Add version migration logic |


## 4. Practical scenario: Data migration plan for e-commerce apps📱
### Scene Description
When users migrate data from old mobile phones to new Hongmeng devices, they need to be synchronized:
- Shopping cart products (database data)
- Collection of pictures (user files)
- Payment password (sensitive data)

### Implementation steps
1. **Backup Phase**:
   ```typescript  
   onBackup() {  
// Backup the database
     backupDbFile('shopping_cart.db');  
// Compress and encrypt pictures
compressUserPhotos('Favorites/', 'photos.tar.gz');
// Encrypt sensitive data separately
     encryptSensitiveData('payment_password', 'encrypted_key');  
   }  
   ```  

2. **Recovery Phase**:
   ```typescript  
   onRestore() {  
// Restore the database first
     restoreDbFile('shopping_cart.db');  
// Decrypt the user file again
decryptAndExtract('photos.tar.gz', 'User/Favorites/');
// Finally load sensitive data (requires secondary verification by user)
     if (await verifyUser()) {  
       loadEncryptedData('payment_password');  
     }  
   }  
   ```  


## 5. Pit avoidance guide ⚠️
### 1. Avoid backup of invalid data
- ❌ Do not back up temporary files (such as cached pictures)
- ✅ Only backup user-generated key data (such as order records, custom settings)

### 2. Handle cross-device differences
```typescript  
// Adjust backup strategy according to device type
const deviceType = await getDeviceType();  
if (deviceType === 'wearable') {  
// The watch is small and only backs up the core data
  backupCoreDataOnly();  
} else {  
// Mobile phone/tablet backup complete data
  backupFullData();  
}  
```  

### 3. Test process specifications
- Simulate application uninstallation → reinstallation, verify that data is fully restored
- Cross-device migration test (such as mobile phone → tablet)
- Extreme scenario test: power outage during backup and insufficient storage space


## Summary: The "Three Principles" of Data Backup
1. **Security first**: Sensitive data must be encrypted, and permission control must be strict
2. **Efficiency priority**: Incremental backup + asynchronous processing, reducing user waiting
3. **Compatible and complete**: The version migration logic is clear and adapted to multiple device scenarios
