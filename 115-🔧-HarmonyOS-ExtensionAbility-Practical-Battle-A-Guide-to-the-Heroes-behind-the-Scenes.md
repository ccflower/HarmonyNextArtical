# 🔧 HarmonyOS ExtensionAbility Practical Battle: A Guide to the Heroes behind the Scenes

> As a developer who was once busy in the development of backend service, today I will reveal the ExtensionAbility of HarmonyOS!When I used it for background service for the first time, my colleague thought I had turned on the "backend hang" but in fact I just used this hidden ability expansion tool right~


## 1. ExtensionAbility: The "behind the scenes team" behind the stage

### 1. What is ExtensionAbility?

Imagine a concert:
- UIAbility is a singer on the stage (front desk interface)
- ExtensionAbility is the behind-the-scenes team (backend services, cards, input methods, etc.)

HarmonyOS's ExtensionAbility core features:
- **Backend Service**: Like a lighting engineer, silently supporting the front desk
- **Service Card**: Like a program list, you can read information without entering the APP
- **Input method**: Like a teleprompter, assisting user input


### 2. Three types: division of labor between the team behind the scenes

| Type | Responsibilities | Life Analogy |
|--------------------|-------------------------------|-----------------------|  
| ServiceExtensionAbility | Backstage services, such as music playback, file download | Backstage band |
| FormExtensionAbility | Service Cards, such as Weather Cards | Concert List |
| InputMethodExtensionAbility | Input Method Extension | Telephone |


## 2. Life cycle: The "work flow" of the team behind the scenes

### 1. Four major stages: from start of work to finish

| Stage | Analog Scenario | Typical Operations |
|--------------|---------------------------|---------------------------|  
| onCreate | Team start-up preparation | Initialization service, loading configuration |
| onForeground | Team enters work | Start processing tasks and applying for resources |
| onBackground | Team suspends work | Release non-essential resources and save progress |
| onDestroy | Team finishes work and leaves | Destroy instances and clean up memory |

### 2. Code Example: A Day in Backstage Music Service

```typescript
import { ServiceExtensionAbility } from '@ohos.app.ability';

export default class MusicService extends ServiceExtensionAbility {
  onCreate(want) {
console.log('Music team starts (service initialization)');
this.initMusicPlayer(); // Initialize the player
  }
  
  onForeground() {
console.log('Music team starts working (service starts)');
this.startPlayback(); // Start playing music
  }
  
  onBackground() {
console.log('Music team pauses (service background)');
this.pausePlayback(); // Pause playback
  }
  
  onDestroy() {
console.log('Music team finishes work (service destruction)');
this.releasePlayer(); // Release resources
  }
}
```  


## 3. ServiceExtensionAbility: The "invisible promoter" of backend services

### 1. Backstage download case: like express sorting center

```typescript
// Download service implementation
import { ServiceExtensionAbility } from '@ohos.app.ability';

export default class DownloadService extends ServiceExtensionAbility {
  private downloadManager: DownloadManager;

  onCreate() {
    this.downloadManager = new DownloadManager();
console.log('Download service starts');
  }

  onRequest(want, startId) {
// Receive download request
    const url = want.parameters?.url;
    if (url) {
      this.downloadManager.startDownload(url);
    }
  }

  onDestroy() {
    this.downloadManager.stopAll();
console.log('Download service stops');
  }
}

// Front desk calls download service
const want = {
  bundleName: 'com.example.downloader',
  abilityName: 'DownloadService'
};
this.context.startAbility(want);
```  

### 2. Communication with UIAbility: Messages transmitted in the front-end and back-end

```typescript
// Backend service sends a message to the front desk
import { getContext } from '@ohos.app.ability';

const eventHub = getContext(this).eventHub;
eventHub.publish('downloadProgress', { progress: 50 });

// The front desk UIAbility receives messages
eventHub.on('downloadProgress', (data) => {
this.downloadProgress = data.progress; // Update progress bar
});
```  


## 4. FormExtensionAbility: Do not open the "Information Window" of the APP

### 1. Weather card case: like a weather board outside the window

```typescript
// Weather card expansion
import { FormExtensionAbility } from '@ohos.app.ability';

export default class WeatherForm extends FormExtensionAbility {
  onShow() {
console.log('weather card display');
this.updateWeatherData(); // Update weather data
  }

  onUpdate(formId) {
console.log(`Weather card update, ID: ${formId}`);
this.refreshWeather(formId); // Refresh card data
  }

  onDestroy() {
console.log('weather card destruction');
  }
}

// Card configuration (config.json)
{
  "extensionAbilities": [
    {
      "name": ".WeatherForm",
      "src": ["WeatherForm.ts"],
      "form": {
"formName": "Weather Card",
        "supportDimensions": ["4*2"]
      }
    }
  ]
}
```  

### 2. Card layout: like a mini information window

```typescript
// Card UI (ArkUI)
@Entry
@Component
struct WeatherForm {
@State weather: WeatherData = { temp: '25℃', condition: 'Xingle' };

  build() {
    Column {
Text('Weather Today')
        .fontSize(16)
        .margin({ bottom: 5 })
      
      Text(`${this.weather.temp} ${this.weather.condition}`)
        .fontSize(24)
        .fontWeight(FontWeight.Bold)
    }
    .width('100%')
    .height('100%')
    .padding(10)
    .backgroundColor(Color.White)
  }
}
```  


## 5. Independent Sandbox: The "confidential workroom" of the team behind the scenes

### 1. Sandbox security mechanism: like a segregated confidential office

- **Process Isolation**: Each ExtensionAbility is independent of the process, one crash does not affect the others
- **Memory isolation**: Independent memory space, preventing data leakage
- **Permission Isolation**: Configure individual permissions, if the weather card cannot access the address book

### 2. Open the sandbox: add a "secret lock" to the backend service

```json
// Configure in module.json5
{
  "extensionAbilities": [
    {
      "name": ".SecureService",
      "src": ["SecureService.ts"],
      "sandbox": {
"enabled": true, // Open the sandbox
"permissions": ["ohos.permission.READ_WEATHER"] // Permissions in the sandbox
      }
    }
  ]
}
```  


## 6. Practical comparison: Before and after using ExtensionAbility

| Scenario | Traditional backend development | ExtensionAbility development |
|--------------|---------------------|---------------------|  
| Backend service stability | A crashed APP hanging | Independent sandbox does not affect the front desk |
| Card update efficiency | You need to open the APP before you update | Card refresh will be automatically refreshed in the background |
| Permission security | Permission chaos is prone to leaks | Sandbox isolation is safer |


## 7. Development skills: "Efficient working method" of the team behind the scenes

### 1. Backend service optimization: "Efficient team" that does not occupy resources

```typescript
onBackground() {
// Release non-essential resources, such as closing tools when the team is suspended
  this.releaseUnusedResources();
  
// Backstage tasks are lightweight, like a team that is light and stands on duty
  this.lightweightMode();
}
```  

### 2. Card performance optimization: "Lightweight display" in small windows

- **Data Cache**: Card data is cached locally, reducing background requests
- **Lazy loading**: The latest data is loaded only when the card is displayed, and sleeps when it is not displayed


## The last thought

When I used ExtensionAbility to make music background for the first time, the front desk cut songs and backend did not stutter. My colleagues thought I used some black technology~ In fact, I just used sandbox isolation and backend service optimization~
