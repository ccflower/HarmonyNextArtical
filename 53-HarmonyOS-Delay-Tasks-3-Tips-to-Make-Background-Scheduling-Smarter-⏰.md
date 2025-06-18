
# HarmonyOS Delay Tasks: 3 Tips to Make Background Scheduling "Smarter" ⏰  

Hi, I'm Xiao L! In HarmonyOS development, delay tasks act like a "smart butler"—they can automatically trigger based on conditions like network status and battery level, avoiding resource waste. Today, I'll teach you three tricks to master delay task scheduling!  


## 1. Delay Tasks: The "Smart Switch" for Conditional Triggering 🔌  
### Core Scenarios  
- **Data-Saving Sync**: Update data only when connected to Wi-Fi  
- **Low Battery Protection**: Pause background downloads when battery level < 20%  
- **Charging-Time Work**: Automatically back up large files when connected to a charger  

### Five Key Trigger Conditions  
| Condition Type       | Optional Values/Ranges         | Sample Code                          |  
|----------------------|---------------------------------|--------------------------------------|  
| **Network Type**     | WIFI/CELLULAR/NONE              | `networkType: NetworkType.WIFI`      |  
| **Battery Level**    | 0-100 (percentage)              | `batteryLevel: 30` (trigger below 30%)|  
| **Charging Status**  | WIRELESS/USB/NONE               | `chargerType: ChargingType.USB`      |  
| **Storage Space**    | >1GB/<500MB, etc.               | `storageFree: 1024` (unit: MB)       |  
| **Scheduled Task**   | Absolute time/periodic time      | `triggerAt: 1690000000` (timestamp)  |  


## 2. Hands-On: Trigger Data Backup via "Wi-Fi + Charging"  
### Step 1: Define Task Conditions  
```typescript  
import { WorkInfo, NetworkType, ChargingType } from '@ohos.workScheduler';  

const backupWork: WorkInfo = {  
  workId: 1001, // Unique identifier  
  bundleName: 'com.example.backup',  
  abilityName: 'BackupTaskAbility',  
  networkType: NetworkType.WIFI, // Only in Wi-Fi environment  
  chargerType: ChargingType.WIRELESS, // Only when wirelessly charging  
  delay: 300, // Delay 300 seconds after conditions are met (avoids immediate resource contention)  
  repeatInterval: 86400, // Execute once daily  
};  
```  

### Step 2: Start Task Scheduling  
```typescript  
function scheduleBackup() {  
  try {  
    workScheduler.startWork(backupWork);  
    console.log('Backup task scheduled, waiting for conditions to trigger');  
  } catch (error) {  
    if (error.code === 1001) {  
      console.log('Task already exists, no need to reschedule');  
    }  
  }  
}  
```  

### Step 3: Handle Task Logic (in ExtensionAbility)  
```typescript  
export default class BackupTaskAbility {  
  onStart(workInfo: WorkInfo) {  
    if (workInfo.workId === 1001) {  
      this.performBackup(); // Execute backup logic  
    }  
  }  

  private async performBackup() {  
    // Double-check network and charging status  
    const isWifi = await checkNetworkType(NetworkType.WIFI);  
    const isCharging = await checkChargingStatus();  
    if (isWifi && isCharging) {  
      await backupToCloud(); // Perform cloud backup  
      workScheduler.stopWork(workInfo.workId); // Free resources after task completion  
    }  
  }  
}  
```  


## 3. System Scheduling: The "Backstage" of Resource Optimization 🧠  
### 1. Task Grouping Strategy  
| App Activity Level | Execution Frequency Limit | Typical Scenarios               |  
|----------------------|---------------------------|---------------------------------|  
| **Active**           | At least once every 2 hours| Instant messaging app message sync|  
| **Frequent**         | At least once every 24 hours| News app auto-update at night    |  
| **Rare**             | At most once every 48 hours | Low-frequency tool app data cleanup|  
| **Restricted**       | No execution               | Apps manually restricted by users|  

### 2. Dynamic Adjustment Mechanism  
- **Low Memory**: Pause non-critical tasks (e.g., image cache updates)  
- **High Temperature**: Delay CPU-intensive tasks (e.g., image processing)  
- **Battery Saver Mode**: Only allow highest-priority tasks (e.g., emergency notifications)  

### 3. Priority Control  
```typescript  
const highPriorityWork: WorkInfo = {  
  ...baseWorkInfo,  
  priority: WorkPriority.HIGH, // Highest priority (default: MEDIUM)  
  overridePolicy: true, // Allow bypassing frequency limits  
};  
```  


## 4. Pitfall Avoidance Guide ⚠️  
### 1. Handling Conditional Conflicts  
- Example: Setting both `batteryLevel: 20` and `chargerType: NONE` (won’t trigger when charging)  
- Solution: Clarify core task conditions to avoid mutual exclusion  

### 2. Repeated Task Scheduling  
- Check task existence before starting:  
  ```typescript  
  workScheduler.getWorkStatus(workId).then(status => {  
    if (status !== WorkStatus.RUNNING) {  
      scheduleBackup();  
    }  
  });  
  ```  

### 3. Resource Release  
- Always call `stopWork` after task completion  
- Avoid time-consuming operations in `onStart`; use asynchronous processing instead  


## 5. Advanced Use Case: Cross-Device Collaborative Scheduling  
### Scenario: Phone + Tablet Collaborative Backup  
1. **Phone**: Set task conditions (Wi-Fi + charging)  
2. **Tablet**: Monitor task triggers and coordinate file uploads  
```typescript  
// Phone-side scheduling  
workScheduler.startWork({  
  ...backupWork,  
  targetDeviceId: 'tablet-device-id', // Specify tablet to execute task  
});  

// Tablet processing logic  
export default class CrossDeviceBackupAbility {  
  onStart(workInfo) {  
    if (workInfo.targetDeviceId === 'tablet-device-id') {  
      // Leverage tablet’s larger storage and computing power for backup  
    }  
  }  
}  
```  


## Summary: The "Three Principles" of Delay Tasks  
1. **Condition Priority**: Use conditional triggers instead of permanent background running  
2. **Frequency Restraint**: Set execution intervals based on user habits  
3. **Dynamic Adaptation**: Adjust task priority according to system resource status