
# Hongmeng system capability call: 3 tricks to unlock the device "superpower" 🔌

Hi~ I am Xiao L!The Hongmeng system has many "superpowers" hidden, and learning to call can make the application become omnipotent in seconds.Today, we use 3 key scenarios to teach you how to quickly get started with core abilities~


## 1. File operation: "Swiss Army Knife" for data storage🔧
### 1. Local file reading and writing (asynchronous best practices)
```typescript
// Read the configuration file
async function readConfig() {  
  const file = await FileIO.open('/data/config.json', 'r');  
  const content = await file.read();  
file.close(); // Important!Free up resources
  return JSON.parse(new TextDecoder().decode(content));  
}  

// Write to log files (append mode)
async function logData(data: string) {  
  const file = await FileIO.open('/data/logs.txt', 'a+');  
  await file.write(new TextEncoder().encode(data + '\n'));  
  file.close();  
}  
```

### 2. Distributed file synchronization (mobile phone → tablet)
```typescript
import { DistributedFile } from '@ohos.distributedFile';  

// Transfer files across devices
async function shareToTablet(filePath: string, deviceId: string) {  
  const df = await DistributedFile.create();  
  await df.open(filePath, 'r');  
  await df.transferTo(deviceId, '/shared/file.pdf');  
  df.close();  
}  
```


## 2. Sensor + Network: Create "perceptual" applications🌐
### 1. Acceleration data (pedometer)
```typescript
import { Sensor, SensorTypeId } from '@ohos.sensor';  

const accelerometer = Sensor.getDefaultSensor(SensorTypeId.ACCELEROMETER);  
let stepCount = 0;  

// Subscribe to the sensor (in onCreate)
accelerometer.subscribe((data) => {  
if (Math.abs(data.x) > 0.8) stepCount++; // Simple step count logic
});  

// Unsubscribe (in onDestroy)
accelerometer.unsubscribe();  
```

### 2. Data upload to the cloud (real-time synchronization)
```typescript
import { Http } from '@ohos.net.http';  

async function uploadStepData(count: number) {  
  const request = Http.createHttp();  
  await request.request({  
    url: 'https://api.example.com/step',  
    method: 'POST',  
    body: JSON.stringify({ count }),  
    headers: { 'Content-Type': 'application/json' }  
  });  
request.destroy(); // Release the connection
}  
```


## 3. Equipment Management: Performance Optimization "Military Advisor Behind the Scenes" 📊
### 1. Obtain device information (dynamic adaptation)
```typescript
import { DeviceInfo } from '@ohos.deviceInfo';  

const device = DeviceInfo.getInfo();  
if (device.type === 'phone') {  
loadSimpleUI(); // Use lightweight interface for mobile phones
} else {  
loadFullUI(); // Full-function interface for flat panel/car computer
}  
```

### 2. Memory monitoring (anti-stuttering)
```typescript
import { MemoryManager } from '@ohos.memory';  

// Check memory every 5 seconds
setInterval(() => {  
  const { usedRate } = MemoryManager.getMemoryInfo();  
if (usedRate > 80%) clearCache(); // More than 80% of the caches are cleaned
}, 5000);  
```


## Key reminder ⚠️
1. **Permission must be added**: Sensitive capabilities (such as sensors) must be declared in `config.json`
   ```json
   { "reqPermissions": [{ "name": "ohos.permission.GET_SENSOR_DATA" }] }  
   ```  
2. **Async processing**: `async/await` is required for file/network operations, and do not block the main thread
3. **Close when used up**: Sensor subscription and network connection are released in `onDestroy`


## Summary: Ability to call "three tricks"
1. **File operation**: Use `FileIO` locally, and use `DistributedFile` across devices.
2. **Sensor + Network**: Subscribe to the data first, then upload it with `Http`
3. ** Device adaptation**: Dynamic adjustment of logic according to `DeviceInfo`
