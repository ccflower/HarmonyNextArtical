# 🏷️ Huawei Hongmeng Stage model practical combat: building a super application that combines devices and collaborates

> As a developer who has been busy in multi-device development, today I want to share the ultimate practical battle of the Stage model!The smart home APP made with this solution can control mobile phones, tablets, and watches at the same time. Now, the "clearance secrets" of cross-device collaboration are fully disclosed~


## 1. Multi-device Collaboration: The "Distributed Stage" of the Stage Model

### 1. Equipment role division (analogous to band division)

| Device Type | Role Positioning | Stage Model Application |
|------------|-------------------|---------------------------|  
| Mobile | Lead singer (interactive core) | Main Stage, manage global status |
| Tablet | Guitarist (large screen display) | Substage Stage, extended display content |
| Watch | Drummer (data acquisition) | ExtensionAbility, real-time data acquisition |
| Smart speakers | Bassist (voice interaction) | ServiceExtension, backend service |


### 2. Core capabilities of distributed stages

- **Components cross-device migration**: Stage is like a mobile stage, and components can "tour" between devices
- **Real-time data synchronization**: Data between the Stage of each device is like a tacit cooperation between band members
- **Task Intelligent Scheduling**: Assign tasks according to device capabilities, such as calculating the heart rate of the watch, and displaying the results on the mobile phone


## 2. System architecture: "Building a Blueprint" for the distributed stage

### 1. Hierarchical architecture design

```  
┌───────────────┐     ┌───────────────┐     ┌───────────────┐  
│ Mobile Phone Main Stage │──────│ Tablet Sub Stage │──────→│ Watch Extension│
│ (UIAbility)   │     │ (UIAbility)   │     │ (ServiceExt)  │  
└───────────────┘     └───────────────┘     └───────────────┘  
       │                    │                    │  
       ▼                    ▼                    ▼  
┌───────────────┐     ┌───────────────┐     ┌───────────────┐  
│ Distributed Task Scheduling │ │ Data Synchronization Center │ │ Equipment Capability Management │
└───────────────┘     └───────────────┘     └───────────────┘  
```  

### 2. Device discovery and connection combat

```typescript
import { distributedDevice } from '@kit.DistributedDeviceKit';

// Discover peripheral equipment (like a band looking for teammates)
distributedDevice.discoverDevices().then((devices) => {
  devices.forEach((device) => {
console.log(`Discover device: ${device.deviceId}, type: ${device.deviceType}`);
    
// Assign Stage roles according to device type
    if (device.deviceType === 'phone') {
      this.assignMainStage(device);
    } else if (device.deviceType === 'tablet') {
      this.assignExtensionStage(device);
    }
  });
});

// Connect the device
async function connectToDevice(deviceId: string) {
  const result = await distributedDevice.connect(deviceId);
  if (result) {
console.log(`Successfully connected to device: ${deviceId}`);
  }
}
```  


## 3. Stage model cross-device application: component "tournament"

### 1. Main Stage (mobile terminal): Stage Commander

```typescript
import { UIAbility, distributedTask } from '@ohos.app.ability';

export default class MainStage extends UIAbility {
  onCreate() {
console.log('Main Stage starts, starts assignment of tasks');
    
// Assign display tasks to the tablet (like the lead singer to part of the guitarist)
    this.assignTabletTask();
    
// Assign data acquisition tasks to the watch
    this.assignWatchTask();
  }
  
  assignTabletTask() {
// Find available tablets
    distributedDevice.findDeviceByType('tablet').then((tabletId) => {
      if (tabletId) {
// Start Stage across devices (send a show invitation to the tablet)
        const want = {
          deviceId: tabletId,
          bundleName: 'com.example.multidevice',
          abilityName: 'TabletStage'
        };
        distributedTask.startAbility(want);
      }
    });
  }
}
```  

### 2. Substage Stage (tablet): large screen display

```typescript
import { UIAbility } from '@ohos.app.ability';

export default class TabletStage extends UIAbility {
  onStart(want) {
console.log('Plate Stage starts, receives the main Stage task');
    
// Subscribe to the main Stage data (like the guitarist listening to the lead singer's rhythm)
    this.subscribeMainStageData();
  }
  
  subscribeMainStageData() {
    const eventHub = this.context.eventHub;
    eventHub.on('mainStageData', (data) => {
// Update the flat panel display (play the guitar according to the lead vocal rhythm)
      this.updateDisplay(data);
    });
  }
}
```  

### 3. ExtensionAbility (watch end): data acquisition

```typescript
import { ServiceExtensionAbility } from '@ohos.app.ability';

export default class WatchService extends ServiceExtensionAbility {
  onCreate() {
console.log('Watch service starts, starts collecting data');
    
// Acquire heart rate regularly (like drummer beats)
    setInterval(() => {
      const heartRate = this.getHeartRate();
      this.sendToMainStage(heartRate);
    }, 1000);
  }
  
  sendToMainStage(data: number) {
    const eventHub = this.context.eventHub;
    eventHub.publish('heartRateData', data);
  }
}
```  


## 4. Distributed task scheduling: the "conductor" of the stage

### 1. Task assignment strategy (analogous to band conductor)

| Task Type | Assignment Principles | Implementation Method |
|----------------|---------------------------|-------------------------|  
| High computing tasks | Give devices with strong computing power (tablet/mobile phone) | distributedTask.assign |
| Real-time data acquisition | Give to nearby equipment (watch/bracelet) | Priority allocation after equipment is discovered |
| Display tasks | Give large screen devices (flat-panel/TV) | Select equipment based on screen size |


### 2. Task Scheduling Practical Code

```typescript
import { distributedTask } from '@kit.DistributedTaskKit';

class TaskScheduler {
  scheduleTask(taskType: string, data: any) {
// Check the list of available devices (such as conductors to see the status of band members)
    distributedDevice.getOnlineDevices().then((devices) => {
//Select equipment according to the task type (the command decides who will play)
      const targetDevice = this.chooseDeviceByTaskType(taskType, devices);
      
      if (targetDevice) {
// Assign tasks (command command)
        distributedTask.schedule({
          deviceId: targetDevice.id,
          taskName: taskType,
          taskData: data
        }).then(() => {
console.log(`task${taskType} has been assigned to device${targetDevice.id}`);
        });
      }
    });
  }
  
  chooseDeviceByTaskType(taskType: string, devices: Device[]) {
// High computing tasks for tablets
    if (taskType === 'highCompute') {
      return devices.find(d => d.deviceType === 'tablet');
    }
// Collect it in real time for the watch
    if (taskType === 'realtimeCollect') {
      return devices.find(d => d.deviceType === 'wearable');
    }
// Default to mobile phone
    return devices.find(d => d.deviceType === 'phone');
  }
}
```  


## 5. Cross-device UI adaptation: "Variety Magic" of Stage Set

### 1. Responsive layout (different equipment and different sets)

```typescript
@Entry
@Component
struct CrossDeviceLayout {
  @State deviceType: string = 'phone';

  build() {
    Column() {
// Mobile layout (small stage set)
      if (this.deviceType === 'phone') {
Text('Mobile Interface')
          .fontSize(20)
        
Button('Send to Tablet')
          .onClick(() => this.sendToTablet())
      }
// Tablet end layout (large stage set)
      else if (this.deviceType === 'tablet') {
        Grid() {
Text('Big interface of tablet end')
            .gridArea('header')
          
          List() {
// Large list layout...
          }
          .gridArea('content')
        }
        .columnsTemplate('1fr')
        .rowsTemplate('50px 1fr')
        .gridAreas('"header" "content"')
      }
    }
    .width('100%')
    .height('100%')
    .onPageShow(() => {
this.detectDeviceType(); // Change the set of detection device type
    })
  }

  detectDeviceType() {
// Detect the current device type
    this.deviceType = distributedDevice.getLocalDeviceType();
  }
}
```  

### 2. Cross-device component migration (actors change stage)

```typescript
// Migrate components to tablet in the main Stage
function migrateComponentToTablet() {
  const want = {
    deviceId: 'tablet123',
    bundleName: 'com.example.app',
    abilityName: 'TabletAbility',
    parameters: {
componentData: this.componentData // Pass component data
    }
  };
  
// Start the tablet Stage and pass component data (the actor takes props to the tablet stage)
  distributedTask.startAbility(want);
}
```  


## 6. Data synchronization: "Score Sharing" between stages

### 1. Distributed Data Center Design

```  
┌───────────────────┐     ┌───────────────────┐     ┌───────────────────┐  
│ Mobile Data Center │───────→│ Tablet Data Center │──────→│ Watch Data Center │
│ (AppStorage)       │     │ (AppStorage)       │     │ (LocalStorage)     │  
└───────────────────┘     └───────────────────┘     └───────────────────┘  
       │                    │                    │  
       ▼                    ▼                    ▼  
┌───────────────────┐     ┌───────────────────┐     ┌───────────────────┐  
│ Data synchronization service │ │ Conflict resolution module │ │ Local caching policy │
└───────────────────┘     └───────────────────┘     └───────────────────┘  
```  

### 2. Real-time synchronization of practical code

```typescript
import { distributedData } from '@kit.DistributedDataKit';

// Initialize the distributed data center
class DataCenter {
  constructor() {
// Start the data synchronization service (like the band unified score)
    this.initSyncService();
    
// Subscribe to changes in data of each device
    this.subscribeDeviceData();
  }
  
  initSyncService() {
    distributedData.init({
syncInterval: 500, // 500ms synchronize once
conflictResolver: this.resolveConflict // Conflict Resolution Function
    });
  }
  
  subscribeDeviceData() {
// Subscribe to mobile data
    distributedData.subscribe('phoneData', (data) => {
      this.updateAllDevices('phoneData', data);
    });
    
// Subscribe to tablet data
    distributedData.subscribe('tabletData', (data) => {
      this.updateAllDevices('tabletData', data);
    });
  }
  
  updateAllDevices(key: string, data: any) {
// Synchronize data to all devices (band members synchronize scores)
    distributedData.syncToAll(key, data);
  }
}
```  


## 7. Security and Permissions: The "Security Check System" of the Stage

### 1. Cross-device permission control

| Permission type | Control method | Implementation code |
|----------------|---------------------------|-------------------------|  
| Device Access | Whitelist Mechanism | distributedDevice.authorize |
| Data operation permissions | Role-based access control | Custom permission verification |
| Task execution permissions | Dynamic permission application | context.requestPermission|


### 2. Secure communication practice

```typescript
import { distributedSecurity } from '@kit.DistributedSecurityKit';

// Equipment certification (security check admission)
async function authenticateDevice(deviceId: string) {
// Generate temporary key (security check pass)
  const key = await distributedSecurity.generateKey();
  
// Send authentication request
  const result = await distributedSecurity.authenticate(deviceId, key);
  
  if (result) {
console.log(`device${deviceId} authentication passed`);
    return true;
  }
  return false;
}

// Encrypted data transmission (encrypted music score transmission)
function sendEncryptedData(deviceId: string, data: any) {
  const encryptedData = distributedSecurity.encrypt(data);
  distributedData.sendToDevice(deviceId, encryptedData);
}
```  


## 8. Practical case: Smart home cross-device control

### 1. Scene description

- Turn on "Home Mode" on your phone, and at the same time:
✅ Flat panel display welcome interface
✅ Watch reminds that the door lock is open
✅ Smart speakers play welcome voice

### 2. Stage model implementation

```typescript
// Mobile phone main Stage (Commander General)
onClickHomeMode() {
// Send instructions to tablets (Tablet Stage displays the welcome page)
  this.sendToTablet({ type: 'welcomePage' });
  
// Give instructions to watches (watch Extension reminder)
  this.sendToWatch({ type: 'unlockAlert' });
  
// Send commands to the speaker (speaker service plays voice)
  this.sendToSpeaker({ type: 'welcomeVoice' });
}

// Cross-device command sending
sendToDevice(deviceId: string, command: any) {
  const want = {
    deviceId,
    bundleName: 'com.example.smartHome',
    parameters: { command }
  };
  distributedTask.startAbility(want);
}
```  


## 9. Performance optimization: "Efficient operation" of the stage

### 1. Multi-device startup optimization

| Optimization items | Traditional methods | After Stage model optimization | Improvement range |
|----------------|---------------------|---------------------|---------------------|  
| Cross-device startup time | Average 3 seconds | Average 1.2 seconds | 60% |
| Data synchronization delay | Average 800ms | Average 300ms | 62.5% |


### 2. Start the framework optimization code

```typescript
// AppStartup configuration (multi-device startup acceleration)
{
  "startupTasks": [
    {
      "name": "deviceDiscovery",
      "priority": 5,
      "parallel": true,
      "deviceTypes": ["phone", "tablet", "wearable"]
    },
    {
      "name": "dataSyncService",
      "priority": 4,
      "dependsOn": ["deviceDiscovery"]
    }
  ]
}
```  


## The last thought

When I used the Stage model to make multiple device applications for the first time, the data between devices was not synchronized for three days. Now I use a distributed data center + EventHub, and cross-device synchronization is as smooth as local operation. It is recommended that novices start with small scenarios, such as simple collaboration between mobile phones and watches, and then gradually expand.
