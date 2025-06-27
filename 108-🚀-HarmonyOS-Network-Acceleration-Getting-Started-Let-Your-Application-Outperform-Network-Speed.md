# 🚀 HarmonyOS Network Acceleration Getting Started: Let Your Application Outperform Network Speed

> As a developer who was once criticized by customers for "APP loading is like a snail", today I want to share life-saving skills for HarmonyOS network acceleration!After using Network Boost Kit to optimize, the customer said, "It's like I've changed to a 5G package." Come and see the acceleration tips that these novices can understand~


## 1. Network acceleration: install a "turbocharger" for the application

Last week, she installed a grocery shopping app for her mother, and she complained that "refreshing is slower than picking vegetables."In fact, HarmonyOS's network acceleration is like adding turbocharge to the application:
- **Connection Migration**: When cutting 4G from WiFi, it will change lanes without slowing down if you drive
- **Scene recognition**: Automatically determine whether the network is blocked or not, if it is blocked, it will take a detour
- **Quality Assessment**: Real-time view of the network's "fuel consumption", fuel saving and running quickly

This set of toolkit (Network Boost Kit) is hidden in the system. Developers can call a few interfaces to make the application take off quickly~


## 2. Development preparation: Get the "High-speed Pass" first

You must apply for permission before using the acceleration function. For example, you need to get a pass card when you go to the highway:
1. Open the project's `module.json5` and add a line:
```json
{
  "requestPermissions": [
    { "name": "ohos.permission.GET_NETWORK_INFO" }
  ]
}
```  
This permission is like telling the system: "I want to monitor the network, give me a pass~"


## 3. 🚗 Connection and migration: drive and change lanes without slowing down

### Scene analogy
Just like driving from the main road to cut the auxiliary road, connecting and migrating allows applications to keep wired when changing networks.The WiFi in my elevator was cut off. WeChat messages can be sent in seconds when cutting 4G, all depends on this function~

### Key code (3 lines to complete)
```typescript
import { netHandover } from '@kit.NetworkBoostKit';

// Subscribe to the website change notification
netHandover.on('handoverChange', (info) => {
if (info.handoverStart) console.log('Start change the network, keep it safe and don't panic');
if (info.handoverComplete) console.log('Successfully switched the network, continue to rush');
});
```  
Remember to unsubscribe after using it, otherwise you will keep monitoring and consuming power~


## 4. 📡 Network scene recognition: Automatically bypass the "traffic traffic jam section"

### Intelligently judge network status
The system will tell you like navigation: "The network ahead is congested, and it is recommended to slow down."When I was doing a live APP, I detected a network blockage in the subway, and automatically reduced the image quality from 1080P to 720P, and never got stuck in PPT again~

### Core code example
```typescript
import { netQuality } from '@kit.NetworkBoostKit';

// Monitor network scene changes
netQuality.on('netSceneChange', (scenes) => {
  scenes.forEach((scene) => {
    if (scene.scene === 'congestion') {
console.log('The network is traffic jamming, reduce the transmission volume');
    }
    if (scene.weakSignalPrediction) {
console.log('The signal ahead is weak, cache data in advance');
    }
  });
});
```  
For example, if you detect that there are too many WiFi people in the mall, you will automatically load the picture with a blurred version first, and then it will become clearer after the network is ready~


## 5. Practical optimization: My "Speed ​​Takeoff" tips

| Optimization | Practice | Effect |
|----------------|-------------------------------|---------------------|  
| Weak network prediction | Cache data before entering the elevator | Scan news in the elevator without going around |
| Dynamic speed regulation | HD transmission on the Internet, HD transmission on the blocked time | Smooth video playback level +50% |
| Connection multiplexing | Use the same network channel for multiple requests | Save traffic and speed up |


## The last thought

When I used the network to accelerate the first time, I didn’t handle the disconnection and reconnection, which caused the APP to collapse in the subway~ Later, I added "3 reconnections + index backoff". Now my mother’s grocery shopping app is refreshed faster than her picking vegetables, and she also praised me for being "reliable than my son-in-law" 😂
