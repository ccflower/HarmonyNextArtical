# 🚀 HarmonyOS network acceleration advancement: From breaking through weak networks to experiencing ascension practice

> As a developer who wants to smash his phone with an APP on the high-speed rail system, today I want to share the advanced secrets of HarmonyOS network acceleration!After using these techniques to optimize, the customer said, "I don't have to turn around when watching videos in the elevator." Come and take a look at these operations that make the Internet speed "resurrect" ~


## 1. Weak network perception: install a "signal radar" for the application

### 1. Detailed explanation of the three "radar modes"

#### 🌐 System real-time judgment: "Emergency doctor" responding in seconds
- **Principle**: The system will sound the alarm immediately when it sees weakSignal or congestion in NetworkScene like an emergency doctor.
- **Code Example**:
```typescript
netQuality.on('netSceneChange', (scenes) => {
  scenes.forEach((scene) => {
    if (scene.scene === 'weakSignal') {
console.log('Currently weak network! Quickly reduce the video quality to standard definition');
      switchVideoQuality('sd');
    }
  });
});
```  
- **Practical Scenario**: When the subway enters the tunnel, the system detects a weak network in real time, and automatically cuts the live broadcast image quality from 1080P to 480P


#### ⏳ System prediction judgment: "Weather Forecaster" who predicted unknown
- **Principle**: Predict the weak network based on signal trends, and tell you in advance that it is "ripped soon"
- **Code Example**:
```typescript
netQuality.on('netSceneChange', (scenes) => {
  scenes.forEach((scene) => {
    if (scene.weakSignalPrediction) {
console.log('Enter into the weak network after 30 seconds! Cache video for 3 minutes first');
      preloadVideo(180);
    }
  });
});
```  
- **Practical scene**: Before the high-speed rail goes to the cave, the system predicts a weak network, caches the next video in advance, and does not stutter when crossing the cave


#### 🧩 App Custom Judgment: A Customized "Private Butler"
- **Principle**: Set thresholds according to application needs, such as uplink bandwidth <100kbps is considered weak network
- **Code Example**:
```typescript
const MY_WEAK_NET_THRESHOLD = {
minUpBandwidth: 100, // Uplink bandwidth is less than 100kbps is considered weak network
maxDelay: 500 // Delay exceeds 500ms is considered weak network
};
netQuality.on('netQosChange', (qosList) => {
  const qos = qosList[0];
  if (qos.linkUpBandwidth < MY_WEAK_NET_THRESHOLD.minUpBandwidth) {
console.log('Custom weak network! Pause large file upload');
    pauseBigFileUpload();
  }
});
```  
- **Practical Scenario**: File transfer APP finds insufficient uplink bandwidth, suspends high-definition video upload, and prioritizes text messages


### 2. Scene Selection Guide

| Mode | Suitable for scenes | Not suitable for scenes |
|--------------|---------------------------|-------------------------|  
| Real-time judgment | Games, live broadcast (response in seconds) | Background download (no real-time sound) |
| Prediction judgment | Video playback, navigation (preparation can be made in advance) | Emergency news (can't wait to predict) |
| Custom judgment | Professional tools (such as surveying and mapping APP) | General APP (complex settings) |


## 2. Application transmission experience feedback: Let the system understand your "pain points"

### 1. Case: The "complain mechanism" of music app

When I was making a music app last week, I found that users were always stuck listening to music in the underground garage.After adding feedback function:
1. When the stutter occurs, the APP calls `reportQoe` to tell the system: "The music playback is stuttering, and the type is a buffering problem."
2. After the system receives it, the next time you cache the music for 5 seconds in a similar scene.
3. User feedback "The underground garage will not be stuck in listening to music"

### 2. Core interface practical combat

```typescript
import { netQuality } from '@kit.NetworkBoostKit';

// Define feedback content: buffering stuttering when playing music
const musicQoe = {
serviceType: 'musicPlay', // Business type: Music playback
qoeType: 'buffering' // Experience type: Buffering problem
};

// Tell the system "The experience here is not good"
netQuality.reportQoe(musicQoe).then(() => {
console.log('has complained about the system's lag problem, etc. to optimize~');
}).catch((err) => {
console.error('Complaint failed:', err);
});
```  

### 3. Feedback Strategy Tips

- **Don't complain**: Only feedback to the same problem within 1 minute will be avoided from being flooded with the system
- **With context**: With feedback, the current network type (WiFi/4G) is provided to help the system accurately optimize
- **Scenario feedback**: Live broadcast stuttering and slow downloading feedback separately, the system will handle it in a targeted manner


## 3. Comprehensive application: Multi-module join forces to "win the cyber war"

### 1. "Sea, Land and Air Joint Operation" of the Social APP

| Module | Weak Net Strategy | Acceleration Effect |
|--------------|---------------------------|-----------------------|  
| Text chat | Real-time judgment of weak network, message compression 50% | Message sending success rate in tunnel +40% |
| Image sending | Compress pictures first when predicting weak networks | The speed of transmission of pictures on weak networks is increased by 1 times |
| Video call | Custom judgment weak network, frame rate reduction | Subway call lag rate from 30% → 10% |


### 2. Data comparison: What is the difference in acceleration whether to open or not?

| Function | Acceleration not turned on | After acceleration on |
|--------------|-----------------------|-----------------------|  
| Post to Moments on the subway | Pictures will be sent in 15 seconds | 8 seconds will be sent and compressed automatically |
| Elevator brush video | Buffer 3 times, 10 seconds each | Only buffer 1 time, 3 seconds to solve |
| Tunnel file sending | Failure rate 50% | Failure rate 5% |


## 4. My ultimate skill of "anti-weak network"

1. **Preload + cache**: Preload 10 messages before entering the elevator. Check the cache first when there is no network.
2. **Dynamic shard**: Split the large file into 1MB small pieces and transmit it, and only the last piece is retransmitted after the network is cut off.
3. **Intelligent downgrade**: When weak network, the video will be automatically cut from "high definition" and "smooth" and priority will be given to the playback.

## The last thought

When I tested the acceleration function on the high-speed rail for the first time, I watched the video from card to PPT to smooth playback, and I was so excited that I almost shouted out~ Now my router is broken and I use my phone hot spots, so I am not panicked when I am on to accelerate the show~
