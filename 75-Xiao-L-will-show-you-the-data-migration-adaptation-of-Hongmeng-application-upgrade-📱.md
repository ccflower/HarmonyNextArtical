
# Xiao L will show you the data migration adaptation of Hongmeng application upgrade 📱

Data migration during application upgrade is the key to ensuring user experience.Through the data migration framework, Hongmeng allows the old version of data to be seamlessly connected to the new version.This article analyzes core components and practical points to help you achieve safe and reliable migration logic~


## 1. Migration scenario: The necessary "data bridge" to upgrade🌉
When the application or system version is updated, two types of data migrations need to be resolved:
- **In-app upgrade**: Old version APK data → New version HAP data (such as migrating from Android compatibility layer to native Hongmeng)
- **System level upgrade**: HarmonyOS old version → HarmonyOS Next (such as file storage path changes)
- **Cross-device migration**: Data synchronization during device replacement (such as mobile phone → tablet)


## 2. Core components: the "three horses" of the migration framework🚗
| Component Name | Function Description |
|------------------------|-------------------------------------------|  
| **BackupExtensionAbility** | Core extension capabilities, implementing `onBackup` and `onRestore` logic |
| **Data migration scheduler** | Automatically trigger the migration process, supporting breakpoint continuous transmission |
| **Migrate Storage Directory** | Temporary storage of backup data (`/data/migrate/tmp/`) |

### Key Lifecycle Callback
```typescript  
export default class DataMigration extends BackupExtensionAbility {  
// Backup of old version data
  async onBackup() {  
// Export old sandbox data (such as SQLite database, user files)
    this.copyFile('/old_app/data.db', '/migrate/tmp/data.db');  
  }  

// Recover new version of data
  async onRestore(version: string) {  
if (version.startsWith('1.')) { // Upgrade from 1.x to 2.0
      this.migrateV1ToV2();  
} else if (version.startsWith('2.')) { // Small version upgrade
      this.migrateConfigs();  
    }  
  }  
}  
```  


## 3. Practical combat: the entire process of cross-version data migration 💾
### 1. Version compatibility judgment
```typescript  
// Get the old version number
const oldVersion = this.getContext().getBundleVersion();  

// Determine whether specific migration logic is required
if (oldVersion < '2.0.0') {  
this.migrateLegacyData(); // Migrate old format data
}  
```  

### 2. Data format conversion (example: JSON → binary)
```typescript  
// Old version of JSON configuration file
const legacyConfig = await this.readFile('/old_config.json');  

// Convert to binary format (new version uses Protocol Buffers)
const protoConfig = this.convertToProto(legacyConfig);  
await this.writeFile('/new_config.pb', protoConfig);  
```  

### 3. Cross-sandbox file migration
```typescript  
// Copy the user picture in the old sandbox to the new sandbox
const oldImagePath = '/data/app/old/files/image.jpg';  
const newImagePath = this.getContext().filesDir + '/image.jpg';  

try {  
  await File.copy(oldImagePath, newImagePath);  
console.log('Image migration is successful');
} catch (error) {  
console.error('Migration failed:', error);
throw new MigrationError('File copy failed');
}  
```  


## 4. Best practice: Avoid "migration traps" ⚠️
### 1. Incremental migration rather than full
```typescript  
// Only migrate change data (such as new user settings)
const diffData = this.calculateDiff(oldData, newData);  
await this.saveDiff(diffData);  
```  

### 2. Transactional Guarantee
```typescript  
// Use transactions to ensure the atomicity of migration steps
async function migrateInTransaction() {  
  try {  
    await this.startTransaction();  
    await this.migrateFiles();  
    await this.updateDatabaseSchema();  
    await this.commitTransaction();  
  } catch (error) {  
await this.rollbackTransaction(); // Rollback on failure
    throw error;  
  }  
}  
```  

### 3. Migrate the test matrix
| Test Scenario | Verification Point |
|-------------------------|-----------------------------------------|  
| Small version upgrade (2.0→2.1) | Whether the configuration file is retained |
| Large version upgrade (1.x→2.0) | Is the data format conversion correct? |
| Cross-device migration (mobile phone → tablet) | Whether multimedia files are fully transferred |
| Migration interrupt recovery | Can I continue to migrate after restart |


## 5. User experience optimization: Make migration "feel free"✨
### 1. Progress visualization
```typescript  
// Update migration progress (display percentage)
this.setMigrationProgress(50); // 50% completed
```  

### 2. Error friendly tips
```typescript  
catch (error) {  
  if (error instanceof StorageError) {  
showToast('Insufficient storage space, please clean it and try again');
  } else {  
showToast('Migration failed, please contact customer service');
  }  
}  
```  

### 3. Automatic backup guarantee
```typescript  
// Automatically back up old data before migration
await this.createBackupSnapshot();  
console.log('Before migration backup was created');
```  


## Summary: The "Four Elements" of Migration and Development
1. ** Version judgment**: Identify the migration type through `BundleVersion`
2. **Format compatibility**: Transitional solutions are required for new and old version data structures
3. **Atomic Operation**: Use transactions to ensure the reliability of the migration process
4. **Test coverage**: Cover all scenario migration test cases
