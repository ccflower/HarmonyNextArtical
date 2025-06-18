
# Hongmeng Embedded UI Extension Component: "Stage Magic" embedded across process interfaces🎭

hello!I am Xiao L, the female programmer who "plays cross-process interface" in Hongmeng UI development~ Do you know?In Hongmeng applications, ordinary UI components are like "single talk shows", and the embedded UI extension component can realize "cross-process stage series" - "changing" the interface of another application into the current page!Today, let’s talk about this “stage magic” that can break through the boundaries of the process, and see how to make the interface embedded as silky as “magic”~


## 1. What are embedded UI extension components?"Cross-border actor" in the interface 🤹♀️
**Core Concept**:
A special UI component that allows the interface of another UIAbility (or even another application) to be embedded in the current application interface, and the two run in independent processes to achieve "interface cross-border and process isolation".

**Core Competencies**:
1. **Span-process interface embed**: Display the interface of application B in the page of application A (such as the weather plug-in embedded in the system desktop)
2. **Security isolation of independent processes**: Embedded components have independent processes, and crashes do not affect the main application
3. **Flexible interactive communication**: Supports bidirectional data transmission of main applications and embedded components

**Typical Scenario**:
- **System-level plug-ins**: Calendar and weather components are embedded in the desktop
- **Application function extension**: E-commerce App Embeds Logistics Tracking Components (From Logistics Application)
- **Multi-Window Collaboration**: Office App Embeds Document Editing Components (From Document Application)


## 2. Three steps of development: build a magic process of "cross-process stage" 🔮
### (I) Create embedded component projects
**1. New component type**
Select "UI Extension Ability" in DevEco Studio to generate the basic code framework:
```typescript
// Embedded Component Core Class
export default class MyEmbeddedAbility extends EmbeddedUIExtensionAbility {
// Triggered when component creation
  onCreate() {
    console.log('[MyEmbedded] onCreate');
  }

// Triggered when session creation (load interface)
  onSessionCreate(want: Want, session: UIExtensionContentSession) {
// Load component interface (corresponding to pages/extension directory)
session.loadContent('extension', { /* Pass parameter */ });
  }
}
```

**2. Configuration component declaration**
Declare an embedded component in `module.json5` and specify the supported embed type:
```json
{
  "abilities": [
    {
      "name": "com.example.EmbeddedAbility",
      "srcPath": "src/ets/abilities/EmbeddedAbility",
      "type": "uiExtension",
      "visible": true,
      "metadata": {
        "uiExtension": {
"supportedTypes": ["embeddedType"] // Supported embedding types
        }
      }
    }
  ]
}
```

### (II) Develop embedded interface (ArkUI implementation)
**Interface file**: `src/ets/pages/extension/Extension.ets`
```typescript
@Entry
@Component
struct ExtensionComponent {
@State message: string = 'Greetings from embedded components ~';

  build() {
    Column() {
      Text(this.message)
        .fontSize(16)
        .margin(10);
Button('Click to notify the main app')
        .onClick(() => {
// Call the main application communication interface
          this.sendMessageToHost();
        })
    }
    .width('100%')
    .height('200px')
    .backgroundColor('#F5F5F5');
  }

// Send a message to the main application (via the public interface)
  sendMessageToHost() {
    (getContext() as EmbeddedUIExtensionAbilityContext)
      .getHostAbility()
.callExtensionMethod('onMessageFromExtension', { content: 'Hello, main application!' });
  }
}
```

### (III) Main application integrated embedded components
**1. Add embedded container in the main interface**
```typescript
// Main application page code
@Entry
@Component
struct MainPage {
  @State embeddedReady: boolean = false;

  build() {
    Column() {
      if (this.embeddedReady) {
// Embed the component container and specify the Want of the target component
        EmbeddedComponent({
          want: {
            bundleName: 'com.example.embedded',
            abilityName: 'MyEmbeddedAbility'
          },
          type: EmbeddedType.EmbeddedUIExtension
        })
        .width('100%')
        .height('300px');
      }
Button('Load embedded components')
        .onClick(() => this.loadEmbeddedComponent());
    }
  }

// Load embedded components
  loadEmbeddedComponent() {
    const want = {
      bundleName: 'com.example.embedded',
      abilityName: 'MyEmbeddedAbility',
parameters: { key: 'value' } // Parameters passed to embedded components
    };
    this.embeddedReady = true;
  }
}
```

**2. Implement cross-process communication (taking data sharing as an example) **
```typescript
// The main application receives embedded component messages
class MainAbility extends UIAbility {
  onWindowStageCreate() {
// Register a communication callback
    this.setUIContent('pages/Main', (component) => {
      component.on('messageFromExtension', (data) => {
console.log('Embedded message received:', data.content);
      });
    });
  }
}
```


## 3. Key technical points: "Code Spell" behind magic✨
### (I) Process isolation and security mechanism
1. **Independent process run**:
Embedded components have independent PIDs, and the main application and component processes interact through **IPC (inter-process communication). One party crashes without affecting the other party.
2. **Permission Control**:
The main application calls the component to declare permissions (such as `ohos.permission.USE_UI_EXTENSION`), and the component needs to apply for permissions separately to access the system resources.

### (II) Comparison of communication methods
| Communication method | Applicable scenarios | Performance | Complexity |
|----------------|------------------------|--------|--------|
| **Data Sharing** | Simple data transfer (such as strings) | High | Low |
| **IPC Interface** | Complex Object Passing | Medium | Medium |
| **File/Database** | Large amount of data or persistent storage | Low | High |

**Recommended Solution**:
- Lightweight interaction → Data Sharing (`setParam` of `UIExtensionContentSession`)
- Complex logic → IPC interface (custom AIDL interface)

### (III) Life cycle management
| Main application operation | Component life cycle callback | Description |
|---------------------|---------------------------|--------------------------|
| Loading components | `onCreate` → `onSessionCreate` | Component initialization |
| Component visibility changes | `onForeground`/`onBackground` | Interface cut-in/cut-out |
| Uninstall component | `onSessionDestroy` → `onDestroy` | Resource Release |


## 4. Practical cases: Weather plug-in is embedded in the system desktop🌤️
### (I) Requirements Analysis
- Plugins for embedded third-party weather applications on the system desktop
- Plugin displays real-time weather, click to refresh data
- Cross-process communication between main application (desktop) and plug-in (weather application)

### (II) Key code implementation
**1. Plug-in (weather application)**
```typescript
// Refresh weather data and notify the main application
Button('Refresh the weather')
  .onClick(() => {
    fetchWeatherData().then((data) => {
// Update the main application display through data sharing
      this.session.setParam('weatherData', data);
    });
  });
```

**2. Main application side (system desktop)**
```typescript
// Receive plug-in data and render
EmbeddedComponent({
  want: { bundleName: 'com.weather.app' },
  type: EmbeddedType.EmbeddedUIExtension
})
.on('weatherData', (data: WeatherData) => {
this.currentWeather = data; // Update the weather data of the main interface
});
```

### (III) Effect display
| Indicators | Traditional Plug-in Solutions | Embedded Component Solutions |
|--------------|--------------------|--------------------|
| Process isolation | Same process (high risk) | Independent process (safety) |
| Interface fluency | Occasional lags | Smooth rendering |
| Resource Occupancy | High (Shared Memory) | Low (Independent Resource) |


## 5. A guide to avoid pits: "Tao List" for Magic Performance ⚠️
### (I) Do not operate the main application UI in embedded components
```typescript
// ❌ Error: The embedded component directly modifys the main application interface
(component as MainComponent).updateUI(); 

// ✅ Correct: Notify the main application to update by itself through the communication interface
this.session.callHostMethod('updateUI', data);
```

### (II) Pay attention to cross-process data type limitations
**Support type**: Basic data type, JSON serializable object
**Do not support types**: functions, complex UI objects (such as Component instances)

### (III) Processing scenarios of component loading failure
```typescript
// ✅ Add exception capture
try {
new EmbeddedComponent({ /* Parameter */ });
} catch (error) {
if (error.code === 1234) { // There is no error code in the component
showToast('Component loading failed, please check configuration');
  }
}
```

### (IV) Avoid memory leaks
**Key Operations**:
- Call `destroy()` when the main application uninstalls the component
- Component releases timers, network connections and other resources in `onSessionDestroy`


## 6. Future trends: a stronger cross-process UI ecosystem🌐
### (I) In-depth integration of "atomic services"
Embedded components will seamlessly support atomized service calls, for example:
- The main application is directly embedded in the "order tracking" interface of the takeaway atomized service
- Use third-party functional components without installing the complete application

### (II) 3D interface embedding
In the future, embedded components may support rendering 3D content (such as AR models) and mixing layouts with 2D elements of the main interface to create an immersive interactive experience.

### (III) Cross-device interface flow
In distributed scenarios, embedded components can be dynamically migrated across devices:
- Document editing component embedded in tablets for mobile apps
- The interface automatically adapts to the screen size and interaction method of different devices


## Summary: "Stage Formula" for embedded components🎬
** Cross-process experience = (interface embedding flexibility × communication efficiency) ÷ process isolation cost**
- Lightweight extension → Priority to data sharing communication
- High performance requirements → Planning IPC interface design in advance
- Long-term maintenance → Establish component life cycle management specifications
