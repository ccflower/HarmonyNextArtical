
# Hongmeng AbilityStage: "Stage Commander" of the Stage model🎬

hello!I am Xiao L, the female programmer who "plays with component management" in the Hongmeng Stage model~ Do you know?On the "stage" of Hongmeng Application, each Stage is an independent performance, and **AbilityStage** is the "commander-in-chief" of this performance - the "core brain" that controls component life cycle, resource allocation and process scheduling!Today, let’s talk about the “heart component” of this Stage model and see how it makes the application run as silky as smooth~


## 1. What is AbilityStage?Stage's "Central Control Room"🚀
**Essential Positioning**:
- "Top Manager" of each Stage module, globally unique
- Responsible for coordinating the life cycle of all components in the Stage (such as UIAbility, Service)
- Control Stage's resource allocation, process management and cross-component communication

**Core Responsibilities**:
1. **Life Cycle Management**: Unified scheduling of the creation, startup and destruction of components in Stage
2. **Resource Center**: Manage the allocation and recycling of memory, files, network and other resources
3. **Process Manager**: Determines which process the component runs in and handles inter-process communication
4. **System Interaction Bridge**: Respond to system events (such as configuration changes, memory alarms)


## 2. Create AbilityStage: The first step to building the "Command Command Center" 🔧
### (I) Manual creation steps
1. **New directory and file**
Create the `abilityStage` folder in the ets directory of Module and add the `AppStage.ets` file:
   ```typescript
   // AppStage.ets
   import { AbilityStage } from '@ohos.app.ability';
   export default class AppStage extends AbilityStage {
// Lifecycle callback
     onCreate() {
       console.log('[AppStage] onCreate');
     }
   }
   ```

2. **Configure module.json5**
Specify the Stage entry in the module configuration:
   ```json
   {
     "module": {
"srcEntry": "ets/abilityStage/AppStage", // Point to the AbilityStage file
       "abilities": [
// Other component declarations...
       ]
     }
   }
   ```

### (II) Relationship with UIAbility
| Role | AbilityStage | UIAbility |
|--------------|-----------------------------|-----------------------------|
| **Scope of action** | Global (whole Stage module) | Local (single interface component) |
| **Creation time** | Only created when Stage starts | Create on demand (multiple instances are allowed) |
| **Core Competencies** | Resource Management, Process Scheduling | Interface Display, User Interaction |

**analogy**:
- AbilityStage → Theater Director (coordinate the entire performance)
- UIAbility → Actor (responsible for the specific program performance)


## 3. Life cycle callback: Director's "Script Timetable" 📅
### (I) Key callback function
| Callback Name | Trigger Time | Typical Use |
|------------------|-----------------------------------|-----------------------------------|
| **onCreate** | Stage creation (HAP first loading) | Global resource initialization (such as database connection) |
| **onAcceptWant** | Receive specified mode startup requests | Handle explicit calls across Stage |
| **onConfigurationUpdated** | System configuration changes (such as language, screen rotation) | Dynamic update of resources (such as multilingual text) |
| **onMemoryLevel** | System memory alarm (low memory/out of memory) | Release non-essential resources (such as cached images) |

### (II) Code example: Respond to system memory changes
```typescript
import { AbilityStage, AbilityConstant } from '@ohos.app.ability';

export default class AppStage extends AbilityStage {
// Memory status monitoring
  onMemoryLevel(level: AbilityConstant.MemoryLevel) {
    switch (level) {
case AbilityConstant.MemoryLevel.LEVEL_HIGH: // Enough memory
this.restoreCaches(); // Restore cache
        break;
case AbilityConstant.MemoryLevel.LEVEL_LOW: // Insufficient memory
this.clearUnusedResources(); // Clean up useless resources
        break;
    }
  }

// Clean up non-essential resources
  private clearUnusedResources() {
// Release the image cache
    ImageCache.getInstance().clear();
// Turn off idle network connection
    NetworkManager.getInstance().closeIdleConnections();
  }
}
```


## 4. Resource management practice: the director’s “resource dispatching technique” �️
### (I) Cross-component resource sharing
**Scenario**: Multiple UIAabilities require sharing user login status
```typescript
// Create a shared state in AbilityStage
export default class AppStage extends AbilityStage {
private static _userInfo: UserInfo; // Global user information

  onCreate() {
// Load user information during initialization (such as stored from local)
    AppStage._userInfo = LocalStorage.get('userInfo');
  }

// Provide a public interface for obtaining user information
  static getUserInfo(): UserInfo {
    return AppStage._userInfo;
  }
}

// Use shared resources in UIAbility
@Entry
@Component
struct MainAbility {
  build() {
Text(`Welcome ${AppStage.getUserInfo().name}`)
      .fontSize(20);
  }
}
```

### (II) Process management strategy
**Default Policy**:
- Lightweight components (such as UIAbility) → Run in the main Stage process
- Heavyweight components (such as background service) → Configurable independent processes

**Configuring standalone process example**:
```json
{
  "abilities": [
    {
      "name": "BackgroundService",
      "type": "service",
"process": "com.example.background" // Independent process name
    }
  ]
}
```

** Advantages**:
- Main process remains lightweight to avoid excessive memory usage
- The crash of independent processes does not affect the main application


## 5. Cross-Stage Communication: Director's "cross-field scheduling" 📞
### (I) Scenario: Stage A calls the component of Stage B
**Step 1**: Declare the receiving method in AbilityStage of Stage B
```typescript
// Stage B's AbilityStage
export default class BStage extends AbilityStage {
  onAcceptWant(want: Want): string {
    if (want.action === 'callBComponent') {
// Execute cross-Stage logic (such as starting a specified UIAbility)
      this.startAbility({ abilityName: 'BAbility' });
      return 'Stage B handled';
    }
    return '';
  }
}
```

**Step 2**: Send a cross-Stage request in Stage A
```typescript
// Stage A's UIAbility
import { wantAgent } from '@ohos.app.ability';

const callBStage = () => {
  const want = {
    deviceId: '',
bundleName: 'com.example.stageB', // Stage B's package name
action: 'callBComponent' // Custom action
  };
  wantAgent.sendWant(want, (err, data) => {
    if (!err) {
console.log('Stage B response:', data);
    }
  });
};
```


## 6. Guide to avoid pits: Director’s “Tao List”⚠️
### (I) Don't operate the UI in AbilityStage
```typescript
// ❌ Error: Directly modify the status of UI components in Stage
export default class AppStage extends AbilityStage {
  onCreate() {
new MainAbility().updateUI(); // Cannot access specific UI instance
  }
}

// ✅ Correct: Notify UIAbility updates through event mechanism
class EventManager {
static onStageEvent(callback: () => void) { /* Implement event subscription */ }
}

export default class AppStage extends AbilityStage {
  onCreate() {
EventManager.emit('stageReady'); // Trigger event
  }
}
```

### (II) Avoid over-dependence on singleton patterns
**Risk**: Global singletons may cause memory leaks or initialization order issues
**Alternatives**:
- Implement lightweight global state using static properties of AbilityStage
- Complex state management adopts Hongmeng's DataAbility or Redux mode

### (III) Declare permissions when handling cross-device scenarios
```json
// Authorization is required when calling Stage across devices
{
  "reqPermissions": [
    {
      "name": "ohos.permission.DISTRIBUTED_SCHEDULE"
    }
  ]
}
```


## 7. Future trend: Smarter Stage management system🤖
### (I) "Elastic Resource Scheduling" Mechanism
Automatically adjust component process priorities according to real-time memory/CPU usage:
- Foreground UI Components → High Priority Process
- Background data synchronization → Low priority process

### (II) "Stateless Stage" support
A lightweight Stage may be launched in the future, allowing simple components to be run without creating AbilityStages, reducing the development costs of micro applications such as atomized services.

### (III) "Visual Stage Monitoring"
It provides a Stage resource monitoring panel in HarmonyOS Studio to display memory usage, process status, and cross-Stage call links in real time to help developers quickly locate performance bottlenecks.


## Summary: AbilityStage's "Director's Law" 🎭
**Stage stability = (life cycle management accuracy × resource scheduling efficiency) ÷ unnecessary overhead**
- Global resource placement, component resource placement
- Travel standard interface across Stage communication to avoid direct dependencies
- Memory sensitive operations are processed in the `onMemoryLevel` callback
