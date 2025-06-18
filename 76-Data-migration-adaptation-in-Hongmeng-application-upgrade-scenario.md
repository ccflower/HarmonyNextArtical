
# Data migration adaptation in Hongmeng application upgrade scenario

During the application upgrade process, the reliability of data migration directly affects the user experience.Through the data migration framework, Hongmeng provides developers with a complete solution to seamlessly migrate old versions of data to the new version.This article will analyze the core component functions and explain the key implementation steps in combination with practical cases.


## 1. Typical scenarios of data migration
### 1. Application version iteration
- **Scenario**: Upgrade from APK application to HAP format, or HarmonyOS application version number spans multiple large versions (such as 1.x→3.0).
- **Requirements**: Migrate user profiles, databases, multimedia data, etc. to the new sandbox path.

### 2. System version upgrade
- **Scenario**: The device is upgraded from HarmonyOS 2.0 to HarmonyOS Next, and the storage path or data format has changed.
- **Requirements**: Data storage specifications adapted to new systems (such as partition adjustments, encryption policy changes).

### 3. Cross-device migration
- **Scenario**: When a user changes the device (such as mobile phone → tablet), the application data needs to be synchronized.
- **Requirements**: Cross-device data synchronization is compatible with version through distributed capabilities.


## 2. Core components: BackupExtensionAbility
The core of the Hongmeng data migration framework is `BackupExtensionAbility`, which realizes backup and recovery logic by rewriting its lifecycle method:

| Method name | Function description | Typical operations |
|----------------|-----------------------------------|-------------------------------------------|  
| `onBackup()` | Old version data backup | Export database and user files to temporary migration directory |
| `onRestore()` | New version data recovery | parse temporary data, convert formats and write to new sandbox |
| `onCleanup()` | Clean after migration | Delete temporary files and free up storage space |

### Version compatibility judgment logic
```typescript  
export default class DataMigration extends BackupExtensionAbility {  
  async onRestore(version: string) {  
// Get the old version number (such as "1.0.0")
    const oldMajorVersion = parseInt(version.split('.')[0]);  
// Large version upgrade (1.x→2.x)
    if (oldMajorVersion < 2) {  
await this.migrateV1ToV2(); // Perform format conversion and path migration
    }  
// Small version upgrade (2.0→2.1)
    else {  
await this.migrateSettings(); // Migrate only configuration files
    }  
  }  
}  
```  


## 3. Practical process: cross-version data migration implementation
### 1. Old data backup (onBackup)
```typescript  
async onBackup() {  
// Backup SQLite database
  const oldDbPath = '/data/app/old/data.db';  
  const backupPath = this.getContext().getExternalFilesDir() + '/migrate/data.db';  
  await File.copy(oldDbPath, backupPath);  
  
// Compress user picture folder
  const imageDir = '/old_app/images/';  
  const zipPath = '/migrate/images.zip';  
  await File.compress(imageDir, zipPath, 'zip');  
}  
```  

### 2. Data format conversion
```typescript  
// Example: Convert old version of JSON configuration to Protobuf format
async migrateConfigFormat(legacyPath: string, targetPath: string) {  
  const jsonData = await File.read(legacyPath, 'utf-8');  
  const protoData = ConfigProto.fromJson(JSON.parse(jsonData));  
  await File.write(targetPath, protoData.toBinary(), 'binary');  
}  
```  

### 3. Cross-sandbox recovery (onRestore)
```typescript  
async onRestore() {  
// Get the new sandbox path
  const newFilesDir = this.getContext().filesDir;  
// Recover the database
  const backupDbPath = '/migrate/data.db';  
  await File.copy(backupDbPath, `${newFilesDir}/app.db`);  
  
// Unzip the image and migrate to a new directory
  const zipPath = '/migrate/images.zip';  
  await File.uncompress(zipPath, `${newFilesDir}/images/`);  
}  
```  


## 4. Best practices and risk avoidance
### 1. Incremental migration optimization
```typescript  
// Migrate only the change file (pass MD5 verification)
async incrementalMigrate(oldDir: string, newDir: string) {  
  const oldFiles = await File.list(oldDir);  
  for (const file of oldFiles) {  
    const oldMd5 = await File.getMd5(`${oldDir}/${file}`);  
    const newMd5 = await File.getMd5(`${newDir}/${file}`);  
    if (oldMd5 !== newMd5) {  
      await File.copy(`${oldDir}/${file}`, `${newDir}/${file}`);  
    }  
  }  
}  
```  

### 2. Transactional Guarantee
```typescript  
// Ensure migration integrity using atomic operations
async migrateInAtomicTransaction() {  
  try {  
// Start a transaction (create a temporary directory)
    const tempDir = await File.createTempDir();  
// Perform migration steps
    await this.migrateDataToTemp(tempDir);  
// Submit transaction (move to target directory)
    await File.move(tempDir, this.getTargetDir());  
  } catch (error) {  
// Roll back the transaction (delete temporary data)
    await File.delete(tempDir);  
throw new MigrationError('Migration failed, rolled back');
  }  
}  
```  

### 3. Migrate the test matrix
| Test Type | Test Cases | Verification Points |
|----------------|-------------------------------------------|-----------------------------------------|  
| **Functional Test** | Small version upgrade (2.0.1→2.0.2) | Whether the configuration file is preserved and whether the database table structure is compatible |
| **Compatibility Test** | Large version upgrade (1.0→3.0) | Are the old format data converted correctly and whether the path is adapted |
| **Stress test** | Restart the application after migration interruption | Is the interruption of breakpoint transmission normal and whether the data is complete |
| **Security test** | Is the data transmitted encrypted during the migration process | Whether sensitive data (such as password) is not stored explicitly |


## 5. User experience optimization strategy
### 1. Visualize progress feedback
```typescript  
// Update migration progress (UI layer synchronous display)
let progress = 0;  
this.setMigrationProgress(progress); // Initial state
// Update progress for each stage completed
progress += 25;  
this.setMigrationProgress(progress);  
```  

### 2. Error handling and bottom-up plan
```typescript  
catch (error) {  
  switch (error.code) {  
    case 'STORAGE_FULL':  
showToast('Insufficient storage space, please clean it and try again');
await this.cleanup(); // Clean up temporary files to free space
      break;  
    case 'PERMISSION_DENIED':  
showToast('Insufficient permissions, please check storage permissions');
      break;  
    default:  
showToast('Migration failed, old data has been automatically restored');
await this.restoreLegacyData(); // Fall back to old version data
  }  
}  
```  

### 3. Automatic backup mechanism
```typescript  
// Automatically create data snapshots before migration
async beforeMigration() {  
  const snapshotPath = await File.createSnapshot(this.getContext().filesDir);  
console.log(`Backup creation successfully: ${snapshotPath}`);
}  
```  


## Summarize
The Hongmeng Data Migration Framework provides a flexible migration logic implementation method through `BackupExtensionAbility`. Developers need to focus on the atomic guarantees of version compatibility judgment, data format conversion and migration processes.Through incremental migration, transaction mechanism and comprehensive testing, user data can be securely and completely migrated to the new version during the upgrade process, providing users with a seamless application upgrade experience.
