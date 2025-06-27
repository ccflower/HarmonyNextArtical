# 📱 HarmonyOS social application network optimization practice: a counterattack guide from disconnection to instant transmission

> As a developer who once couldn't send messages on the subway and was criticized by friends, I want to use my big move today!After using HarmonyOS network acceleration and optimization, social APP sends messages in the elevator faster than friends’ iPhones. These practical skills make social apps “resurrect” the speed of the Internet~


## 1. Multi-network switching: "Network Navigator" for social scenes

### 1. Intelligent switching strategy: choose the way like an old driver

During a shopping mall test last week, I found that the timing of WiFi switching to 4G is too critical:
- **Signal threshold**: Switch when WiFi signal < -75dBm and 4G bandwidth>500kbps
- **Cooldown time**: Do not switch repeatedly within 30 seconds after cutting the net to avoid "repeated jumps"

```typescript
import { netManager } from '@kit.NetworkBoostKit';

let lastSwitchTime = 0;
const COOLDOWN = 30000; // 30 seconds of cooling

function checkNetwork() {
  const now = Date.now();
  if (now - lastSwitchTime < COOLDOWN) return;
  
const wifi = getWifiSignal(); // Assume that the WiFi signal strength is obtained
const cellular = get4GBandwidth(); // Get 4G bandwidth
  
  if (wifi < -75 && cellular > 500) {
    netManager.switchToCellular();
    lastSwitchTime = now;
console.log('Cutted to 4G, network speed takeoff~');
  }
}
```  

### 2. Special handling of chat scenes: messages cannot be "lost halfway"

- **Switching message guarantee**: Save unsent messages locally before cutting the network, and automatically resend them after cutting the network.
- **Video call priority**: Increase the switching threshold during call, and it is better to switch 4G to keep the line

```typescript
// Pre-store messages in the network
netManager.on('handoverStart', () => {
  saveUnsendedMessages();
console.log('Save it well and haven't sent a message, don't be afraid of losing it when changing the network~');
});

// Re-send after cutting the net
netManager.on('handoverComplete', () => {
  resendSavedMessages();
console.log('The network change is successful, send a message again~');
});
```  


## 2. Weak network optimization: The basement can also "send messages in seconds"

### 1. Data compression: "lose weight" for the message

- **Picture compression**: Original image 10MB → compressed to 500KB, the sending speed under weak network is increased by 20 times
- **Text slimming**: Long messages are sent in segments, each segment does not exceed 140 bytes, success rate +30%

```typescript
// Compress image function
async function compressImage(imgData) {
// The mass is reduced to 0.5 and the size is reduced to 720P
  return await imgCompressor.compress(imgData, {
    quality: 0.5,
    maxWidth: 1280
  });
}

// Send long messages in segments
function sendLongMessage(msg) {
  if (msg.length > 140) {
    const chunks = splitMessage(msg, 140);
    sendChunksSequentially(chunks);
  } else {
    sendSingleMessage(msg);
  }
}
```  

### 2. Interface adaptation: "low-key display" when the network is poor

- **Picture placeholder**: When the network is weak, the gray square will be displayed first, and then the network will be changed to high-definition pictures.
- **Video Silence**: Automatically pause automatic playback, click to load

```typescript
// Display the placeholder map when weak network
function showPlaceholderImage() {
  document.getElementById('image').src = 'gray-placeholder.jpg';
  if (isGoodNetwork()) {
    loadHighQualityImage();
  }
}

// Automatic video playback is prohibited
function disableAutoPlay() {
  const videos = document.querySelectorAll('video');
  videos.forEach(video => {
    video.autoplay = false;
    video.addEventListener('click', loadVideo);
  });
}
```  


## 3. Scenario optimization: "Exclusive acceleration" on different pages

### 1. Chat page: Message priority is "Green Channel"

- **Strategy**: Only text + low-definition avatars are loaded on the chat page, high-definition pictures are available for you to refresh the website
- **Code**:

```typescript
// Set a policy when entering the chat page
app.on('pageChange', (page) => {
  if (page === 'chat') {
    setNetworkStrategy('chat');
console.log('Chat page strategy: priority text, slow loading of pictures');
  }
});

function setNetworkStrategy(strategy) {
  if (strategy === 'chat') {
setPriority('text'); // Text priority
setImageQuality('low'); // Low-definition image
  }
}
```  

### 2. Moments: "Batch Loading" when the Internet is in trouble

- **Strong Net**: Load 10 items at a time, preload pictures
- **Wife Net**: 2 items at a time, click to load the original image

```typescript
function loadFriendsCircle() {
  const networkQuality = getNetworkQuality();
  let loadCount = networkQuality === 'good' ? 10 : 2;
  
  fetchPosts(loadCount).then(posts => {
    posts.forEach(post => {
      if (networkQuality === 'good') {
loadPostImages(post); // The strong network directly loads the image
      } else {
post.showThumbnail(); // Weak network display thumbnail first
      }
    });
  });
}
```  


## 4. Performance monitoring: "Do a physical examination" for the network

### 1. Key indicators: Measure network speed like blood pressure

| Indicators | Normal range | Warning value | Optimization action |
|--------------|----------------|----------------|---------------------------|  
| Request success rate | >95% | <80% | Check weak network reconnection strategy |
| Response Time | <500ms | >2000ms | Optimized Server Interface |
| Picture loading speed | <1500ms | >5000ms | Enhanced image compression |

### 2. Problem troubleshooting: 3-step positioning network speed is slow

1. **Look at the indicator**: Check the request success rate first, if it is less than 80%, it means there is a problem with the weak network reconnection.
2. **Check log**: See which type of request is slow, such as slow video loading, optimize compression
3. **Test environment**: Comparing competitors under the same network and determining whether it is a code problem or a network problem


## 5. Practical effect: Data will not lie

| Scenario | Before Optimization | After Optimization |
|--------------|---------------------|---------------------|  
| Send a message from the subway | Send it in 10 seconds | Send it in 2 seconds |
| Elevator Moments | 3 failed to load 5 items | 4 successful items |
| Underground garage video | 5 times/minute | 1 time/minute |


## The last thought

I remember when I was testing in the basement for the first time, the message was not sent out and was ridiculed by my colleagues~ After optimization, they asked me if I had secretly enabled "privileges" to the APP~ In fact, I just used the network acceleration function of HarmonyOS right~
