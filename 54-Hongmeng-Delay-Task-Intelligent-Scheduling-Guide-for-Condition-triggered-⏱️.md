
# Hongmeng Delay Task: "Intelligent Scheduling" Guide for Condition-triggered ⏱️

Hi~ I am Xiao L!In Hongmeng development, delayed tasks are like "time housekeeper", which can automatically execute tasks under specific conditions such as Wi-Fi connection and charging, saving both power and traffic.Today, we will use 3 key scenarios to help you play back-end scheduling~


## 1. Delay task: "Energy-saving switch" triggered on demand 🔦
### Core Advantages
- **Conditional Driver**: Only perform tasks when network, power and other conditions are met
- **System-level optimization**: Avoid background resident and reduce resource usage
- **Scene adaptation**: Perfectly match the requirements of "non-emergency but specific environments"

### Typical application scenarios
| Scene | Trigger Conditions | Task Content |
|---------------------|---------------------------|---------------------------|  
| **Data synchronization under Wi-Fi** | Connect to Wi-Fi network | Synchronize large files or high-definition pictures |
| ** System update when charging** | Connect the charger and the power is >80% | Download and install the new version of the application |
| **Low battery pause task** | Battery capacity <15% and not charged | Stop background music playback or positioning |


## 2. Trigger condition configuration: precisely control task execution 🎯
### Key parameter analysis
```typescript  
import { WorkInfo, NetworkType, ChargingType } from '@ohos.workScheduler';  

const workInfo: WorkInfo = {  
workId: 1001, // Unique task ID
bundleName: 'com.example.app', // Application package name
abilityName: 'SyncTaskAbility', // Ability that hosts the task
networkType: NetworkType.WIFI, // Only Wi-Fi environment execution
batteryLevel: 30, // Trigger when the battery is less than 30% (need to be combined with the charging state)
chargerType: ChargingType.NONE, // Non-charged state
triggerAt: 169000000, // Absolute time trigger (time stamp, unit seconds)
repeatInterval: 86400, // Repeat execution every day (in seconds)
delay: 600, // Delay 10 minutes after the condition is met (avoiding immediate resource preemption)
};  
```  

### Example of combination conditions: "Wi-Fi and Charge" triggers backup
```typescript  
const backupWork: WorkInfo = {  
  ...baseWorkInfo,  
  networkType: NetworkType.WIFI,  
chargerType: ChargingType.WIRELESS, // When charging wirelessly
priority: WorkPriority.HIGH, // High priority (priority over normal tasks)
};  
```  


## 3. System scheduling mechanism: "behind the scenes logic" of resource optimization 🧠
### 1. Application grouping and execution frequency
| Application activity level | Lower limit of execution interval | Typical scenarios |
|----------------|----------------|------------------------|  
| **活跃**       | 2小时          | 社交App消息同步        |  
| **Frequently used** | 24 hours | News App Night Content Update |
| **Rarity of use** | 48 hours | Tool App Cache Cleanup |
| **Restricted** | Prohibited execution | Applications manually restricted by users |

### 2. Dynamic adjustment strategy
- **When memory is insufficient**: Pause non-essential tasks (such as preloading of pictures)
- **High temperature environment**: Delayed CPU-intensive tasks (such as image compression)
- **Low battery mode**: Only emergency tasks (such as uploading of health monitoring data)

### 3. Task priority control
```typescript  
// High priority tasks (break through the packet frequency limit)
const urgentWork: WorkInfo = {  
  ...baseWorkInfo,  
  priority: WorkPriority.HIGH,  
overridePolicy: true, // Allow enforced
};  
```  


## 4. Practical code: three-step intelligent synchronization tasks
### 1. Define task configuration
```typescript  
function createSyncWork() {  
  return {  
    workId: 2001,  
networkType: NetworkType.WIFI, // Wi-Fi only
batteryLevel: 50, // Power capacity ≥50%
    abilityName: 'DataSyncAbility',  
repeatInterval: 3600, // Check once an hour
  };  
}  
```  

### 2. Start task scheduling
```typescript  
function scheduleWork() {  
  const workInfo = createSyncWork();  
  try {  
    workScheduler.startWork(workInfo);  
console.log('Synchronous task scheduled');
  } catch (error) {  
    if (error.code === 1002) {  
console.log('Task already exists, no need to repeat scheduling');
    }  
  }  
}  
```  

### 3. Task logic implementation (in ExtensionAbility)
```typescript  
export default class DataSyncAbility {  
  onStart(workInfo: WorkInfo) {  
    if (workInfo.workId === 2001) {  
      this.performSync();  
    }  
  }  

  private async performSync() {  
// Double verification (avoid misjudgment of system conditions)
    const isWifi = await checkNetworkConnection();  
    const isSufficientBattery = await checkBatteryLevel();  
    if (isWifi && isSufficientBattery) {  
      await syncDataToCloud();  
workScheduler.stopWork(workInfo.workId); // The task is completed, freeing resources
    }  
  }  
}  
```  


## 5. Pit avoidance guide ⚠️
### 1. Conditional conflict handling
- Avoid setting of mutex conditions at the same time (such as `batteryLevel: 20` and `chargerType: WIRELESS`)
- Solution: Define the core conditions, use the combination of `logic and/or`

### 2. Task repetition problem
- Check the task status before dispatching:
  ```typescript  
  workScheduler.getWorkStatus(workId).then(status => {  
    if (status !== WorkStatus.SCHEDULED) {  
      scheduleWork();  
    }  
  });  
  ```  

### 3. Resource Release Specifications
- `stopWork` must be called after the task is completed
- Avoid creating global variables or long connections in tasks


## 6. Advanced gameplay: Cross-device task collaboration🌐
### Scenario: Mobile phone + car computer collaborative download
1. **Mobile terminal**: Set task conditions (parking status + car charging)
2. **Car machine terminal**: Receive tasks and perform large file downloads
```typescript  
// Scheduling tasks to the vehicle on the mobile phone
const crossDeviceWork: WorkInfo = {  
  ...downloadWork,  
targetDeviceId: 'car-device-id', // Specify the execution device
networkType: NetworkType.CELLULAR_5G, // Allow 5G network (car-mounted scenarios)
};  

// Car machine processing logic
onStart(workInfo) {  
  if (workInfo.targetDeviceId === 'car-device-id') {  
useCarStorageToDownload(); // Use the car computer to increase storage space
  }  
}  
```  


## Summary: The "Four Principles of Efficiency" for Delay Tasks
1. **Accurate conditions**: Use the least combination of conditions to achieve the goal
2. **Frequency restraint**: Do not break through system grouping restrictions unless necessary
3. **Double Verification**: Verify the environment conditions again before the task is executed
4. **Cross-end collaboration**: Combining distributed capabilities to expand scheduling scenarios
