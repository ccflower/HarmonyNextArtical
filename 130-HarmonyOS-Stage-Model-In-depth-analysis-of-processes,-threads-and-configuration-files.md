# HarmonyOS Stage Model: In-depth analysis of processes, threads and configuration files

## 1. The process architecture of the Stage model: the balancing of isolation and efficiency

### 1. Core design of process model

| Process Type | Include Components | Advantages | Application Scenarios |
|----------------|---------------------------|-----------------------|-------------------------|  
| Unified Process | UIAbility/ServiceExtension | Convenient Data Sharing | Interaction with Components in the Module |
| Independent process | FormExtension/Input method | Safe isolation | Independent functions such as card/Input method |
| WebView process | Web component rendering | Anti-UI lag | Web page content loading |
| Custom process | System application exclusive | Resource isolation | High-permission system services |

### 2. Process isolation mechanism

- **Memory isolation**: Independent processes have exclusive memory space, such as FormExtension crashes, does not affect the main process
- **Permission Isolation**: Configure independent permissions through `sandbox`, if the input method is only available for text permissions
- **Security Sandbox**: API 12+ is enabled by default, can be configured in `module.json5`:
  ```json
  {
    "extensionAbilities": [
      {
        "name": ".FormExt",
        "sandbox": {
          "enabled": true,
          "permissions": ["ohos.permission.FORM_DISPLAY"]
        }
      }
    ]
  }
  ```  


## 2. Thread model: "Task Scheduling Center" behind the stage

### 1. Three-thread collaborative working mode

#### ① Main thread (UI thread)
- **Responsibilities**: UI rendering, event processing, life cycle management
- **Tao**: Time-consuming operations (such as network requests) are prohibited, otherwise it will cause UI to be stuck

#### ② TaskPool Worker thread
- **Features**: The system automatically manages thread pools and dynamically adjusts the number of threads
- **Applicable**: Medium-time-consuming tasks such as network requests, data analysis, etc.
- **Code Example**:
  ```typescript
  import { taskPool } from '@ohos.thread';
  
// Submit the task to TaskPool
  taskPool.submit(() => {
const data = fetchNetworkData(); // Network request
EventHub.publish('dataReady', data); // Notify the main thread
  });
  ```  

#### ③ Worker thread
- **Features**: Developers manually manage life cycles and support long-term operation
- **Applicable**: Time-consuming tasks such as file reading and writing, big data processing, etc.
- **Code Example**:
  ```typescript
// Create Worker thread
  const worker = new Worker('worker.js');
  
// Inter-thread communication
  worker.onmessage = (event) => {
console.log('Worker data received:', event.data);
  };
  
worker.postMessage('Start Processing File');
  ```  

### 2. Inter-thread communication: EventHub actual combat

```typescript
// Main thread subscribes event
EventHub.on('networkData', (data) => {
this.uiData = data; // Update UI
});

// TaskPool thread release event
taskPool.submit(() => {
  const data = await fetch('https://api.harmony.com/data');
  EventHub.publish('networkData', data);
});
```  


## 3. Configuration file: Stage's "Stage Building Blueprint"

### 1. app.json5: application-level global configuration

| Key fields | Functions | Configuration examples |
|----------------|-----------------------|---------------------------|  
| bundleName | App-unique ID | "com.example.harmonyapp" |
| version | version number | "1.0.0" |
| reqPermissions | Global Permission Statement | ["ohos.permission.INTERNET"]|

### 2. module.json5: module-level fine configuration

#### ① Process configuration
```json
{
  "module": {
"process": "com.example.harmonyapp.main", // Custom process name
    "thread": {
      "taskPool": {
"coreSize": 4, // Number of core threads
"maxSize": 8, // Maximum number of threads
"keepAliveTime": 60000 // Thread survival time (ms)
      }
    }
  }
}
```  

#### ② Component process isolation configuration
```json
{
  "extensionAbilities": [
    {
      "name": ".SecureExt",
"process": "com.example.harmonyapp.secure" // Independent process
    }
  ]
}
```  


## 4. Performance optimization: "Efficient operation tips" on the stage

### 1. Three principles of process optimization

1. **Lightweight Process**: No independent processes are created unless necessary to reduce memory usage
2. **Process Reuse**: Extension shared process of the same type (such as multiple Form use the same process)
3. **Sandbox is enabled on demand**: Only enable sandbox for security-sensitive components

### 2. Thread Scheduling Optimization

| Optimization | Practice | Effect |
|----------------|-------------------------------|---------------------|  
| Priority settings | taskPool.submit(task, { priority: 2 }) | Priority execution of important tasks |
| Dynamic adjustment of thread count | Dynamic setting of coreSize according to device performance | Adapting to different hardware devices |
| Time-consuming task split | Large task split into multiple small tasks | Avoid long-term occupation of threads |


## 5. Practical cases: thread management in high concurrency scenarios

### Image loading optimization solution

```typescript
import { taskPool, Worker } from '@ohos.thread';

class ImageLoader {
  private worker: Worker;

  constructor() {
    this.worker = new Worker('imageWorker.js');
    this.worker.onmessage = this.processImageResult.bind(this);
  }

// Main thread call
  loadImage(url: string) {
// Get thumbnails from TaskPool first
    taskPool.submit(() => {
      const thumbnail = getThumbnail(url);
      EventHub.publish('showThumbnail', thumbnail);
      
// Let Worker process the original image
      this.worker.postMessage(url);
    });
  }

  processImageResult(event: MessageEvent) {
    const fullImage = event.data;
    EventHub.publish('showFullImage', fullImage);
  }
}
```  


## 6. Safety and stability best practices

### 1. Process security configuration

```json
{
  "module": {
    "reqPermissions": [
      {
        "name": "ohos.permission.READ_USER_STORAGE",
        "usedScene": {
          "when": "inUse",
"description": "Read photo album pictures"
        }
      }
    ],
    "sandbox": {
      "enabled": true,
"restrictedCapabilities": ["fileSystem"] // Restrict file system access
    }
  }
}
```  

### 2. Thread exception handling

```typescript
// TaskPool exception capture
taskPool.submit(() => {
  try {
    dangerousOperation();
  } catch (error) {
console.error('TaskPool exception:', error);
    EventHub.publish('errorOccurred', error);
  }
});

// Worker exception listening
worker.onerror = (error) => {
console.error('Worker error:', error.message);
worker.terminate(); // Destroy Worker in exception
};
```  


## Summary: "Stage Management Philosophy" of Stage Model

The HarmonyOS Stage model achieves a balance between performance and security through the "unified process + independent process" architecture; the three-threaded model cooperates with EventHub communication to solve the problem of UI lag; the fine control of the configuration file allows developers to customize the optimal operating environment according to the scenario.Mastering these core mechanisms can build high-performance applications that are coordinated like symphonys and present a smooth user experience on the HarmonyOS stage.
