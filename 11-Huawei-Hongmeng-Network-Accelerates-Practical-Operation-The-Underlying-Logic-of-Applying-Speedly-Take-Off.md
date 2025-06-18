# Huawei Hongmeng Network Accelerates Practical Operation: The Underlying Logic of Applying "Speedly Take Off"

Hello everyone!I am Xiao L, the female programmer who "sews and patches" in Hongmeng Network Optimization~ Have you ever encountered the WiFi suddenly breaks while watching videos and the screen is stuck in PPT?Or cut the data traffic while playing games and get dropped directly?Today, let’s talk about Hongmeng’s network acceleration artifact - Network Boost Kit. Let’s see how it allows applications to “silently transition” when switching networks, and “turn the trend against the wind” under a weak network~


## 1. The "core engine" of network acceleration: Network Boost Kit

### (I) Four "secret weapons" functions
1. **Connection migration: "Seamless Bridge" for Network Switching**
- Scene: Go out from home WiFi, automatically cut to 4G/5G, and video playback will not be stuttered~
- Principle: Establish a new network connection in advance, complete data channel migration before the old connection is disconnected, just like a smooth transition when high-speed rail is replaced~

2. **Network scene recognition: "label" the network**
- Ability: Real-time identification of "congestion", "weak signals", "high delay" and other scenarios, and even predict that it will enter the weak network area soon~
- Application: When the live broadcast app detects congestion, it will automatically reduce the resolution to ensure smoothness~

3. **Network quality assessment: "Doing a physical examination" for the network**
- Indicators: Get data such as bandwidth, delay, packet loss rate and other data in real time, just like doing "blood routine" for the network ~
- Purpose: The game app dynamically adjusts the frame rate according to the delay to reduce the probability of lag ~

4. **Transmission Feedback: "Optimization Dialogue" between the application and the system**
- Mechanism: The application tells the system that "the file transmission was slow just now", the system adjusts the scheduling strategy, and the next transmission is accelerated~
- Analog: The takeaway rider told the dispatcher to "this road is traffic jam", and the system will automatically plan a faster route next time ~


## 2. Development preparation: permissions and environment configuration

### (I) Authorization application: Obtain the "Accelerating Pass"
1. Add network information permissions in `module.json5`:
   ```json
   {
     "module": {
       "requestPermissions": [
         {
           "name": "ohos.permission.GET_NETWORK_INFO"
         }
       ]
     }
   }
   ```
*Note*: This is the basic permission, and additional application is required for sensitive operations (such as switching networks) `ohos.permission.CHANGE_NETWORK_STATE`~

### (II) Development environment construction
- **ArkTS API**: `@kit.NetworkBoostKit` module (API 9+)
- **Tool Chain**: DevEco Studio 4.0+, make sure to check the "Network Boost Kit" component
- **Testing Equipment**: It is recommended to connect to WiFi and data network at the same time to simulate switching scenarios~


## 3. Connection migration: say goodbye to "study network switching"

### (I) Core interface: Monitoring switch life cycle
```typescript
import { netHandover } from '@kit.NetworkBoostKit';

// 1. Subscribe to the switch event
netHandover.on('handoverChange', (info) => {
  if (info.handoverStart) {
// Switch start: Pause non-critical tasks and prepare to switch
console.log('The network will be switched soon, the current connection:', info.oldNetId);
Pause video buffering();
  } else if (info.handoverComplete) {
// Switching is completed: Recover the task and adjust the transmission policy
console.log('Switch to new network:', info.newNetId);
Recover video buffering (info.newBandwidth); // Adjust speed according to new network bandwidth
  }
});

// 2. Unsubscribe (when the page is destroyed)
onDestroy() {
  netHandover.off('handoverChange');
}
```

### (II) Best practice: "Preconnection" optimization
- Create a new network connection before switching:
  ```typescript
// When the WiFi signal is detected to be weak, activate the data network in advance
if (current network signal strength < threshold) {
netHandover.preConnect('cellular'); // Pre-connect to cellular network
  }
  ```
- Switch to the "cost-performance" network first:
  ```typescript
// Compare network quality and choose networks with low latency and cheap traffic
const network list = await netHandover.getAvailableNets();
const Optimal network = network list.sort((a, b) => a.latency - b.latency)[0];
netHandover.switchToNet(optimal network.id);
  ```


## 4. Network scene recognition: Let the application "perceive the environment"

### (I) Real-time scene monitoring
```typescript
import { netQuality } from '@kit.NetworkBoostKit';

netQuality.on('netSceneChange', (scenes) => {
  scenes.forEach((scene) => {
    switch (scene.scene) {
case 'congestion': // Network congestion
Reduce the image resolution (50%); // Original image transforms compressed image
        break;
case 'weakSignal': // Weak signal
Enable data compression(); // Compress data before transmission to reduce traffic
        break;
case 'highLatency': // High latency
Increase game frame buffer (); // Preload more screens to prevent lag
        break;
    }
  });
});
```

### (II) Weak signal prediction: "Prepare for the future"
```typescript
// When the system predicts that it is about to enter the weak signal area
if (scene.weakSignalPrediction?.probability > 80%) {
console.log('It is expected to enter the weak network in 5 minutes, cache data in advance');
Cache the next episode of video(); // Download subsequent content in advance
}
```


## 5. Network quality assessment: Use data to "drive optimization"

### (I) Obtain real-time quality indicators
```typescript
const quality = await netQuality.getNetQuality(netId);
console.log(`Current network: download speed ${quality.downloadSpeed}Mbps, delay ${quality.lateency}ms`);

// Dynamically adjust video clarity according to bandwidth
if (quality.downloadSpeed ​​< 1) {
Set video clarity ('standard definition');
} else if (quality.downloadSpeed ​​> 5) {
Set video clarity ('4K');
}
```

### (II) Historical data statistics
```typescript
const historical data = await netQuality.getHistoricalQuality(netId, {
startTime: new Date().getTime() - 3600*1000, // Last 1 hour
interval: '15min' // Statistics every 15 minutes
});

//Analyze network fluctuations trends
Historical data.forEach((stat) => {
  if (stat.latency > 500) {
console.log(`[${new Date(stat.timestamp)}] The network delay soars, needs to be optimized`);
  }
});
```


## 6. Transmission feedback: Create "adaptive" applications

### (I) Feedback transmission experience
```typescript
// After the file transfer is completed, tell the system the transfer result
netQuality.reportTransferExperience({
netId: the current network.id,
success: whether it is successful,
duration: time-consuming transmission,
bytesTransferred: Number of transmitted bytes
});

// Based on feedback, the system will prioritize the optimized network next time
```

### (II) Optimization of typical scenarios
1. **Live broadcast lag optimization**
- Detected stutter (transmission time >2 seconds) → Feedback system → System switch to low-latency network (such as 5G)

2. **Maximum file transfer acceleration**
- Transmission speed <100KB/s → Feedback System → The system enables multipath transmission (using both WiFi and Bluetooth)


## 7. Practical cases: "Multi-network aggregation" accelerates

### (I) Scene: High-definition live broadcast outdoors
- **Problem**: The bandwidth of a single network (such as 4G) is insufficient, and the live broadcast screen is blurred and stuttered
- **Solution**: Use mobile phone 4G and WiFi hotspots at the same time, aggregate bandwidth

### (II) Code implementation
```typescript
// 1. Get the available network list
const nets = await netHandover.getAvailableNets();
const 4G network = nets.find(n => n.type === 'cellular');
const WiFi network = nets.find(n => n.type === 'wifi');

// 2. Start multi-network aggregation
netHandover.startAggregation([4G network.id, WiFi network.id]);

// 3. Real-time monitoring of aggregation bandwidth
netQuality.on('netQualityChange', (quality) => {
console.log('Aggregate bandwidth:', quality.aggregateBandwidth);
Set the live broadcast code rate (quality.aggregateBandwidth * 0.8); // Leave 20% margin
});
```

### (III) Effect comparison
| Scenario | Single network transmission speed | Aggregate transmission speed | Live broadcast clarity |
|--------------|----------------|--------------|------------|
| Outdoor Weak 4G | 500KB/s | 1.2MB/s | HD |
| Indoor congestion WiFi | 800KB/s | 1.5MB/s | Ultra-clear |


## 8. Avoiding pits: FAQs and solutions

### (I) Insufficient permissions lead to invalid function
- **Phenomenon**: Calling the interface returns `PERMISSION_DENIED`
- **Solution**: Check whether `module.json5` declares all necessary permissions, such as:
  ```json
  {
    "name": "ohos.permission.CHANGE_NETWORK_STATE",
    "usedScene": {
      "abilities": ["com.example.app.MainAbility"],
      "when": "inUse"
    }
  }
  ```

### (II) Connection migration is not timely
- **Phenomenon**: There is obvious lag when switching networks
- **optimization**:
- Pre-connect to the new network 3 seconds in advance (`preConnect`)
- Pause non-critical tasks immediately in the `handoverStart` callback

### (III) Scene recognition delay
- **Phenomenon**: Network congestion has occurred, but the application has not adjusted in time
- **optimization**:
- Shorten the listening interval (default 1 second, can be set to 500ms)
  ```typescript
  netQuality.on('netSceneChange', (scenes) => { ... }, { interval: 500 });
  ```


## Last chat
The core of network acceleration is to allow applications to "perceive network changes and dynamically adjust strategies."Hongmeng's Network Boost Kit allows developers to achieve complex network optimization with a few simple lines of code through a "combination punch" of connecting migration, scene recognition, quality evaluation and transmission feedback.Next time, let’s talk about “how to use Hongmeng’s traffic management to do “black technology for streaming”, such as automatically compressing pictures and merging network requests – remember to follow!😉

(Please indicate the source and original author Xiao L when reprinting. Violators... Let your application be loaded forever under the weak network! Just kidding~)
