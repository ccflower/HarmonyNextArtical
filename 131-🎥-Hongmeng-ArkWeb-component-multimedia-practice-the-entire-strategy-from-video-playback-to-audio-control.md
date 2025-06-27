# 🎥 Hongmeng ArkWeb component multimedia practice: the entire strategy from video playback to audio control

> As a developer who has strayed in cross-platform multimedia development, today we will reveal the multimedia capabilities of ArkWeb components!When I first used ArkWeb to implement local video takeover, my colleague thought I used a native player, but in fact I just mastered this magical tool for the integration of Web and native~


## 1. Video playback: Web and native "stage switching"

### 1. Play native videos on the web

```typescript
import { webview } from '@ohos.web.webview';

@Entry
@Component
struct VideoPage {
  private controller: webview.WebviewController = new webview.WebviewController();

  build() {
    Column {
// Load a web page with video
      Web({
        src: 'https://harmonyos.com/video-demo',
        controller: this.controller
      })
      .width('100%')
      .height(300)
      
Button('Enable native player')
        .onClick(() => this.enableNativePlayer())
    }
    .width('100%')
    .padding(20)
  }

// Enable native player takeover
  enableNativePlayer() {
    this.controller.enableNativeMediaPlayer({
      enable: true,
shouldOverlay: true // Native player overwrites the web page
    });
    
// Register native player to create callbacks
    this.controller.onCreateNativeMediaPlayer((handler, mediaInfo) => {
      if (mediaInfo.type === webview.MediaType.VIDEO) {
return new HarmonyVideoPlayer(handler, mediaInfo); // Return to the native player instance
      }
      return null;
    });
  }
}
```  

### 2. Native player implementation (core interface)

```typescript
// Native video player implementation
class HarmonyVideoPlayer implements webview.NativeMediaPlayerBridge {
  private handler: webview.NativeMediaPlayerHandler;
private nativePlayer: VideoPlayer; // Native video component

  constructor(handler: webview.NativeMediaPlayerHandler, mediaInfo: webview.MediaInfo) {
    this.handler = handler;
    this.nativePlayer = new VideoPlayer();
    this.nativePlayer.setSource(mediaInfo.uri);
  }

// Receive playback control of web components
  play() {
    this.nativePlayer.play();
this.handler.handleStatusChanged(webview.PlaybackStatus.PLAYING); // Notify the web status
  }

  pause() {
    this.nativePlayer.pause();
    this.handler.handleStatusChanged(webview.PlaybackStatus.PAUSED);
  }

// Update play area
  updateRect(x: number, y: number, width: number, height: number) {
    this.nativePlayer.setPosition(x, y, width, height);
  }

// Free up resources
  release() {
    this.nativePlayer.release();
  }
}
```  


## 2. Audio playback: "sound collaboration" between the backend and the frontend

### 1. Basics of web audio playback

```javascript
// Web-side JavaScript code
const audio = new Audio('https://harmonyos.com/bg-music.mp3');
audio.loop = true; // Loop play
audio.volume = 0.5; // Volume 50%
audio.play().catch(err => {
console.error('audio playback failed:', err);
});
```  

### 2. Native audio takeover (backstage playback scenario)

```typescript
import { webview } from '@ohos.web.webview';

@Entry
@Component
struct AudioPage {
  private controller: webview.WebviewController = new webview.WebviewController();

  build() {
    Column {
      Web({
        src: 'https://harmonyos.com/audio-demo',
        controller: this.controller
      })
      .width('100%')
      .height(200)
      
Button('Enable native audio')
        .onClick(() => this.enableNativeAudio())
    }
  }

  enableNativeAudio() {
    this.controller.enableNativeMediaPlayer({
      enable: true,
ShouldOverlay: false // Audio does not need to overwrite the UI
    });
    
    this.controller.onCreateNativeMediaPlayer((handler, mediaInfo) => {
      if (mediaInfo.type === webview.MediaType.AUDIO) {
        return new HarmonyAudioPlayer(handler, mediaInfo);
      }
      return null;
    });
  }
}
```  

### 3. Background audio player (supports lock screen playback)

```typescript
class HarmonyAudioPlayer implements webview.NativeMediaPlayerBridge {
  private handler: webview.NativeMediaPlayerHandler;
private audioService: AudioService; // Background audio service

  constructor(handler: webview.NativeMediaPlayerHandler, mediaInfo: webview.MediaInfo) {
    this.handler = handler;
    this.audioService = new AudioService();
    this.audioService.setSource(mediaInfo.uri);
    this.audioService.setOnPlayStatusChanged(this.onStatusChange.bind(this));
  }

  play() {
    this.audioService.play();
  }

  pause() {
    this.audioService.pause();
  }

// Key: Handle audio state changes
  private onStatusChange(status: AudioStatus) {
    let playbackStatus: webview.PlaybackStatus;
    switch (status) {
      case AudioStatus.PLAYING: playbackStatus = webview.PlaybackStatus.PLAYING; break;
      case AudioStatus.PAUSED: playbackStatus = webview.PlaybackStatus.PAUSED; break;
      default: playbackStatus = webview.PlaybackStatus.STOPPED;
    }
    this.handler.handleStatusChanged(playbackStatus);
  }
}
```  


## 3. Camera and microphone: The "sensory interface" of the web

### 1. Call the camera on the web page (scan the code scene)

```javascript
// Web page JS code: Request camera permissions
navigator.mediaDevices.getUserMedia({
  video: { width: 640, height: 480 },
  audio: false
}).then(stream => {
  const video = document.getElementById('qrCamera');
  video.srcObject = stream;
  video.play();
  
// Scan code logic...
}).catch(err => {
console.error('Camera permission denied:', err);
});
```  

### 2. Application-side permission processing (key configuration)

```typescript
import { webview } from '@ohos.web.webview';

@Entry
@Component
struct CameraPage {
  private controller: webview.WebviewController = new webview.WebviewController();

  build() {
    Web({
      src: 'https://harmonyos.com/qr-scanner',
      controller: this.controller
    })
    .onPermissionRequest((event) => {
// Process camera permission request
      if (event.request.getName() === 'ohos.permission.CAMERA') {
event.request.grant(); // Authorized camera
      } else if (event.request.getName() === 'ohos.permission.MICROPHONE') {
event.request.grant(); // Authorize microphone
      }
    })
    .width('100%')
    .height('100%')
  }
}
```  

### 3. Configuration file permission statement

```json
{
  "module": {
    "reqPermissions": [
      {
        "name": "ohos.permission.CAMERA",
        "usedScene": {
          "when": "inUse",
"description": "You need to use a camera to scan the code"
        }
      },
      {
        "name": "ohos.permission.MICROPHONE",
        "usedScene": {
          "when": "inUse",
"description": "Microphone is required for voice input"
        }
      }
    ]
  }
}
```  


## 4. Multimedia optimization: "Behind the Scenes" of smooth experience

### 1. Video playback optimization strategy

| Optimization items | Web playback plan | Native takeover plan |
|----------------|-------------------------|-----------------------|  
| Stuck processing | Adaptive bit rate | Hardware decoding |
| Backstage playback | Limited by web page life cycle | Continuous playback of independent processes |
| Power Consumption Control | Browser Automatic Management | Custom Power Consumption Policy |

### 2. Audio background playback implementation

```typescript
// Native audio service (backend mode)
class BackgroundAudioService {
  private audioPlayer: AudioRenderer;
  private isBackground: boolean = false;

  init(uri: string, isBackground: boolean) {
    this.isBackground = isBackground;
    this.audioPlayer = new AudioRenderer();
    this.audioPlayer.setSource(uri);
    
    if (isBackground) {
      this.setupBackgroundMode();
    }
  }

  private setupBackgroundMode() {
// Configure background audio parameters
    this.audioPlayer.setAudioUsage(AudioUsage.MEDIA);
this.audioPlayer.setKeepAlive(true); // Keep active in the background
  }
}
```  


## 5. Practical cases: The "fusion stage" between live web broadcast and native barrage

### 1. Architectural Design

```  
┌───────────────┐     ┌───────────────┐     ┌───────────────┐  
│ Web Live Page │──────→│ ArkWeb Component │─────→│ Native Barrage Layer │
│ (Video Streaming + Basic Control) │ │ (Video Takeover) │ │ (Same-layer rendering) │
└───────────────┘     └───────────────┘     └───────────────┘  
```  

### 2. Key code implementation

```typescript
// Live page component
@Entry
@Component
struct LivePage {
  private controller: webview.WebviewController = new webview.WebviewController();
  @State danmakuList: string[] = [];

  build() {
    Stack() {
// Live web video (native takeover)
      Web({
        src: 'https://harmonyos.com/live-stream',
        controller: this.controller
      })
      .width('100%')
      .height('100%')
      
// Native barrage layer (floating above the video)
      Column {
        ForEach(this.danmakuList, (text) => {
          Text(text)
            .fontSize(18)
            .fontColor(Color.White)
            .backgroundColor('#50000000')
            .padding(5)
            .margin({ top: 10 })
        })
      }
      .width('100%')
      .height('100%')
    }
    .width('100%')
    .height('100%')
    .onPageShow(() => {
      this.initLive();
    })
  }

  initLive() {
// Enable native video takeover
    this.controller.enableNativeMediaPlayer({
      enable: true,
      shouldOverlay: true
    });
    
// Register barrage reception event
    this.controller.on('danmaku', (data) => {
      this.danmakuList.push(data);
      if (this.danmakuList.length > 20) {
this.danmakuList.shift(); // Limit the number of barrages
      }
    });
  }
}
```  


## 6. Compatibility and security: Multimedia's "Protective Shield"

### 1. Format compatibility scheme

| Format | Web native support | Native takeover support | Recommended solutions |
|--------------|---------------|---------------|---------------------|  
| MP4 | ✅ | ✅ | Priority Web Playback |
| WebM | ✅ | ✅ | Select on Demand |
| AVI | ❌ | ✅ | Must be taken over natively |
| FLV | ❌ | ✅ | Native Takeover |

### 2. Security Policy

- **Domain Whitelist**: Restricting loadable media resource domains
- **HTTPS Force**: All media resources are loaded using HTTPS
- **Dynamic application for permissions**: Dynamic application for permissions when using camera/microphone


## Summary: The "Convergence Way" of ArkWeb Multimedia

The ArkWeb component achieves the seamless integration of the Web and native multimedia capabilities through the dual-mode architecture of "web native playback + local takeover".Developers can choose according to the scenario:
- Simple video/audio: directly use web page native playback
- High performance requirements: Enable native takeover (video hardware decoding, audio background playback)
- Interaction enhancement: combine native components (barrage, special effects) to achieve innovative experience
