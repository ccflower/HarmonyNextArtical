
# Data migration adaptation in Hongmeng application upgrade scenario

When an application is upgraded, the reliability of data migration directly affects the user experience.Through the data migration framework, Hongmeng provides developers with a complete solution to seamlessly migrate old versions of data to new versions.This article analyzes the core component functions and explains the key implementation steps in combination with actual combat.


## 1. Typical scenarios of data migration
### 1. Application version iteration
When the application upgrades from APK to HAP format, or the version number spans a larger version (such as 1.x→3.0), it is necessary to migrate user configuration files, databases, multimedia data, etc. to the new sandbox path.

### 2. System version upgrade
When upgrading a device from an older version of HarmonyOS to HarmonyOS Next, it needs to be adapted to storage path adjustments (such as partition changes) or data format upgrades (such as encryption policy changes).

### 3. Cross-device migration
When users change devices (such as mobile phones → tablets), they need to achieve cross-device synchronization and version compatibility through distributed capabilities.


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
// Example: Convert JSON configuration to Protobuf format
async migrateConfigFormat(legacyPath: string, targetPath: string) {  
  const jsonData = await File.read(legacyPath, 'utf-8');  
  const protoData = ConfigProto.fromJson(JSON.parse(jsonData));  
  await File.write(targetPath, protoData.toBinary(), 'binary');  
}  
```  

### 3. Cross-sandbox recovery (onRestore)
```typescript  
async onRestore() {  
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
    if (oldMd5 !== newMd5) await File.copy(`${oldDir}/${file}`, `${newDir}/${file}`);  
  }  
}  
```  

### 2. Transactional Guarantee
```typescript  
// Atomic operations ensure migration integrity
async migrateInAtomicTransaction() {  
  try {  
    const tempDir = await File.createTempDir();  
await this.migrateDataToTemp(tempDir); // Migrate to temporary directory
await File.move(tempDir, this.getTargetDir()); // Atomic submission
  } catch (error) {  
await File.delete(tempDir); // Roll back on failure
throw new MigrationError('Migration failed, rolled back');
  }  
}  
```  

### 3. Migrate the test matrix
| Test Type | Test Cases | Verification Points |
|----------------|---------------------------|-------------------------------------|  
| Functional Test | Small version upgrade (2.0.1→2.0.2) | Configuration file retention, database table structure compatibility |
| Compatibility testing | Large version upgrade (1.0→3.0) | Old format data conversion, path adaptation |
| Stress test | Restart the application after migration interruption | Breakpoint transmission is normal and data is complete |
| Security testing | Sensitive data encryption transmission | Passwords, etc. are not stored explicitly |


## 5. User experience optimization strategy
### 1. Visualize progress feedback
```typescript  
let progress = 0;  
this.setMigrationProgress(progress); // Initial state
// Update progress in each stage
progress += 25;  
this.setMigrationProgress(progress);  
```  

### 2. Error handling and bottom-up plan
```typescript  
catch (error) {  
  switch (error.code) {  
    case 'STORAGE_FULL':  
showToast('Insufficient storage space, please clean it and try again');
await this.cleanup(); // Clean up temporary files
      break;  
    default:  
showToast('Migration failed, old data has been automatically restored');
await this.restoreLegacyData(); // Rewind old data
  }  
}  
```  

### 3. Automatic backup mechanism
```typescript  
// Create a data snapshot before migration
async beforeMigration() {  
  const snapshotPath = await File.createSnapshot(this.getContext().filesDir);  
console.log(`Backup creation successfully: ${snapshotPath}`);
}  
```  


## Summarize
The Hongmeng Data Migration Framework provides flexible migration logic implementation methods through `BackupExtensionAbility`.Developers need to focus on version compatibility judgment, data format conversion and migration atomicity, and ensure the safe and complete migration of user data through incremental migration, transaction mechanism and comprehensive testing.In the future, distributed data services can be combined with optimized real-time synchronization and conflict resolution of cross-device migration.
