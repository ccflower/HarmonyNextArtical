# �chang️ HarmonyOS AbilityStage Practical Practical: Strategy of "Stage Director" of Stage Model

> As a developer who was once confused in the development of Stage model, today I want to reveal the core mysteries of AbilityStage!When I first used it to manage multiple components, my colleagues thought I had learned "stage magic", but in fact I just mastered the central controller of this Stage model.


## 1. AbilityStage: "Stage Director" of Stage Model

### 1. What is AbilityStage?

Imagine a large stage play:
- AbilityStage is the director of the entire Stage, who is responsible for the "birth, old age, sickness and death"
- Each Stage has and only one AbilityStage, like each performance has only one director

Core responsibilities:
- **Component Lifecycle Management**: Decide when UIAbility and ExtensionAbility will "get on stage and get off the stage"
- **Resource Allocation**: Allocate "props resources" (memory, files, etc.) to each component
- **Process Management**: Manage component processes, like directors and actors


### 2. Three core roles

| Function | Stage Analogy | Development Scenario |
|--------------|---------------------------|---------------------------|  
| Lifecycle Management | Director shouts "Start", "Pause" and "End" | Control page display hidden |
| Resource allocation | Split props for actors | Allocate memory and file handles |
| Process Management | Arrange actor schedule | Manage component processes without conflict |


## 2. Create AbilityStage: Set up a "Director's Office"

### 1. Four-step director creation (practical steps)

#### ① Create a new director’s office (catalog)
Create a new `abilitystage` folder in the ets directory

#### ② Write a director's script (code file)
```typescript
// MyAbilityStage.ets
import { AbilityStage } from '@ohos.app.ability';

export default class MyAbilityStage extends AbilityStage {
  onCreate() {
console.log('Director's onboarding (Stage creation)');
// Initialize stage resources, like the director arranges the set
  }
  
  onAcceptWant(want) {
console.log('Director receives performance requirements');
return 'MyAbilityStage'; // Return the instance ID
  }
}
```  

#### ③ Report the director’s name (configuration file)
```json
// module.json5
{
  "module": {
"srcEntry": "ets/abilitystage/MyAbilityStage.ets", // Point to the director's office
    "abilities": [
      {
        "name": ".MainStage",
        "src": ["MainAbility.ets"]
      }
    ]
  }
}
```  

#### ④ Director on duty (call)
```typescript
// Application portal
import { startAbilityStage } from '@ohos.multimodalInput';

startAbilityStage(MyAbilityStage); // The director starts working
```  


## 3. Life cycle: Director's "Stage Board"

### 1. Four key instructions (callback functions)

| Callback Functions | Director Actions | Development Scenarios |
|----------------|---------------------------|---------------------------|  
| onCreate | shout "Each department is in place" | Initialize global resources |
| onAcceptWant | Receive "performance requirements" | Specify instance mode startup component |
| onConfigurationUpdated | Received "Stage Change Notification" | Adapted to horizontal and vertical screens and language switching |
| onMemoryLevel | Received "Resource Tight Alert" | Release Resources when Memory is insufficient |

### 2. Practical Code: Director's Day

```typescript
export default class MyAbilityStage extends AbilityStage {
  onCreate() {
console.log('The director starts working, arranges the set');
this.initGlobalResources(); // Initialize global resources
  }
  
  onAcceptWant(want) {
console.log(`Startup requirement received: ${want.abilityName}`);
return 'MainStageInstance'; // Return the instance unique identifier
  }
  
  onConfigurationUpdated(config) {
console.log(`Stage Change: ${config.orientation}`);
this.adaptLayout(config); // Adapt Layout
  }
  
  onMemoryLevel(level) {
console.log(`Memory Alert: ${level}`);
this.releaseUnusedResources(); // Release resources
  }
}
```  


## 4. Resource Management: Director’s “Props Warehouse”

### 1. Memory management practice: warehouse inventory clearance

```typescript
onMemoryLevel(level) {
console.log(`Memory tension level: ${level}`);
  
  if (level === MemoryLevel.LevelHigh) {
console.log('Enough memory, normal operation');
  } else if (level === MemoryLevel.LevelMedium) {
this.releaseCache(); // Clear cache
  } else if (level === MemoryLevel.LevelLow) {
this.releaseAllUnused(); // Release all non-essential resources
  }
}
```  

### 2. Global resource sharing: props reuse

```typescript
//Save shared resources in onCreate
onCreate() {
// Save global image resources, like warehouse sharing props
  AppStorage.SetOrCreate('sharedImage', loadBigImage());
}

// Get resources from the component
const sharedImage = AppStorage.Get('sharedImage');
```  


## 5. Designated instance mode: Director's "exclusive actor"

### 1. Scene: Need a specific actor (component instance)

For example, when opening multiple chat windows, each window is an independent instance:

```typescript
// Director handles exclusive actor requests
onAcceptWant(want) {
  if (want.abilityName === 'ChatAbility') {
    const chatId = want.parameters?.chatId;
return `ChatInstance_${chatId}`; // Generate a unique instance name by chat ID
  }
  return super.onAcceptWant(want);
}

// Start exclusive actors
const want = {
  abilityName: 'ChatAbility',
  parameters: { chatId: '12345' }
};
context.startAbility(want);
```  


## 6. Practical comparison: Before and after using AbilityStage

| Scenarios | Traditional Development | AbilityStage Development |
|--------------|---------------------|---------------------|  
| Multi-component management | Manual control of life cycle | Director automatic coordination |
| Resource release | Easy memory leak | Unified release without missing |
| Instance Mode | Difficult to implement specified instances | Built-in support |


## 7. Director’s “Efficient Work Method”

### 1. Resource preloading: prepare props in advance

```typescript
onCreate() {
// Preload common resources, like the director preparing common props in advance
  this.preloadResources([
    'common-image.png',
    'default-style.css'
  ]);
}
```  

### 2. Configuration change optimization: Quickly change the set

```typescript
onConfigurationUpdated(config) {
// When switching horizontal and vertical screens, only update the layout and not rebuild the components
  if (config.orientation === DisplayOrientation.LANDSCAPE) {
    this.updateLandscapeLayout();
  } else {
    this.updatePortraitLayout();
  }
}
```  


## The last thought

When I first used AbilityStage to manage 3 UIAabilities, the memory usage was 30% less than the traditional mode. My colleague asked me if I had learned "memory magic" secretly~ In fact, it was just to let the director (AbilityStage) manage resources uniformly~
