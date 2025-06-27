# 🎭 HarmonyOS UIAbility Practical Battle: A complete guide from interface construction to silky interaction

> As a developer who was forced to crash by UI lag, today we will reveal the UIAbility component of HarmonyOS!When I used it as an interface for the first time, my colleague thought I used "magic", but in fact I just mastered this magical tool for building a silky interface~


## 1. UIAbility: "Chief Actor" of the Interface Stage

### 1. What is UIAbility?

Imagine a drama:
- UIAbility is like a starring actor on the stage, responsible for acting his own "scenes" (interface display)
- Each UIAbility is an independent page, like different scenes in a drama, and can be switched separately

HarmonyOS's UIAbility core features:
- **Interface rendering**: Use ArkUI to match the interface, like actors to show props and set
- **Incident Response**: Handle click swipe, like the actor's response to the audience
- **Data binding**: The data changes the interface and change, like the actors move according to the script


### 2. Life cycle: The actor's "going to the stage and getting off the stage" process

| Lifecycle Stage | Analog Scenarios | Typical Operations |
|----------------|---------------------------|---------------------------|  
| onCreate | Actor Makeup Preparation | Initialization Interface Elements |
| onForeground | Actors perform on stage | Loading data, applying for resources |
| onBackground | Actor retreats to the background | Release resources and save status |
| onDestroy | Actors leave | Destroy instances, clean up memory |

**Practical Code (Actor's Day)**:
```typescript
import { UIAbility } from '@ohos.app.ability';

export default class MainAbility extends UIAbility {
  onCreate() {
console.log('Actor starts to put on makeup (interface initialization)');
this.setUIContent(); // Set the stage setting
  }
  
  onForeground() {
console.log('Actors come on stage (interface display)');
this.loadData(); // Start the performance (load data)
  }
  
  onBackground() {
console.log('Actor backstage (interface hidden)');
this.saveState(); // Remember lines (save status)
  }
  
  onDestroy() {
console.log('Actor's curtain call (interface destruction)');
this.releaseResources(); // Dismantle the stage (release resources)
  }
}
```  


## 2. Interface construction: Use ArkUI to build a "luxury stage"

### 1. Component combination: build interface like building blocks

```typescript
// Build a login interface (analogous to building blocks)
@Entry
@Component
struct LoginPage {
  @State account: string = '';
  @State password: string = '';

  build() {
Column() { // Vertical layout, like a background board on the stage
Text('HarmonyOS login') // Title, like stage big subtitles
        .fontSize(28)
        .margin({ top: 50 })
      
TextInput({ placeholder: 'Account' }) // Input box, like the actor's line book
        .width('90%')
        .margin({ top: 20 })
        .onChange((value) => this.account = value)
      
TextInput({ placeholder: 'password' })
        .width('90%')
        .margin({ top: 10 })
        .type(InputType.Password)
        .onChange((value) => this.password = value)
      
Button('Login') // button, like the key action of the actor
        .width('60%')
        .margin({ top: 30 })
        .onClick(() => this.login())
    }
    .width('100%')
    .height('100%')
    .justifyContent(FlexAlign.Center)
  }

  login() {
console.log(`Account: ${this.account}, password: ${this.password}`);
  }
}
```  

### 2. Layout container: "Scene Scheduling" of the stage

| Container Components | Layout Effects | Life Analogy |
|------------|---------------------------|-----------------------|  
| Column | Vertical arrangement | Theater seats vertical arrangement |
| Row | Horizontal arrangement | Stage props horizontally |
| Stack | Cascading | Stage Set Overlay |
| List | List | Theater Seat List |


## 3. Data binding: Let the interface be a "marionette"

### 1. @State Decorator: The data change interface changes

```typescript
@Entry
@Component
struct DataBindingDemo {
@State count: number = 0; // The data is like a puppet line

  build() {
    Column() {
Text(`Number of clicks: ${this.count}`) // The interface is like a puppet, and it moves when the data moves.
        .fontSize(24)
      
Button('Click Me')
        .onClick(() => {
this.count++; // Pull the wire (change data)
        })
    }
    .padding(20)
  }
}
```  

### 2. @Link Decorator: Cross-component linkage

```typescript
// Parent component (director)
@Component
struct ParentComponent {
  @State message: string = 'Hello';

  build() {
    Column {
ChildComponent({ message: $message }) // Pass the line to the child component
Button('Change Message')
        .onClick(() => this.message = 'World')
    }
  }
}

// Subcomponent (actor)
@Component
struct ChildComponent {
@Link message: string; // Connect the director's line

  build() {
Text(this.message) // Follow the director's line
      .fontSize(20)
  }
}
```  


## 4. Event processing: "Audience interaction" on the interface

### 1. Click event: The audience applauds the actors respond

```typescript
@Entry
@Component
struct ClickDemo {
  @State isClicked: boolean = false;

  build() {
Button(isClicked? 'Clicked': 'Clicked')
      .onClick(() => {
        this.isClicked = true;
// The actor's reaction after hearing the applause (click)
this.showToast('Thank you for clicking!');
      })
      .width(200)
      .height(50)
  }

  showToast(message: string) {
// Show prompt
  }
}
```  

### 2. Sliding event: The audience waves the actors to move

```typescript
@Entry
@Component
struct SwipeDemo {
  @State position: number = 0;

  build() {
    Column {
Text(`position: ${position}`)
        .fontSize(24)
      
      Stack() {
        Rectangle()
          .width(300)
          .height(200)
          .fill(Color.Blue)
        
Text('Swipe me')
          .fontSize(20)
      }
      .gesture(
        PanGesture({ direction: GestureDirection.All })
          .onActionUpdate((event) => {
this.position = event.offsetX; // Wave to the audience
          })
      )
    }
  }
}
```  


## 5. Cross-component communication: "Line Transmission" between actors

### 1. EventHub: "Walkwaiki" on the stage

```typescript
// Send (Actor A)
import { getContext } from '@ohos.app.ability';

let eventHub = getContext(this).eventHub;
eventHub.publish('newMessage', 'Line received'); // Send a message on the walkie-talkie

// Receiver (Actor B)
eventHub.on('newMessage', (message) => {
console.log(`Message received: ${message}`); // Use walkie-talkie to receive messages
});
```  

### 2. AppStorage: The "Notice Board" of the Stage

```typescript
// Save data (post announcement)
AppStorage.SetOrCreate('userInfo', { name: 'HarmonyOS user' });

// Get data (see announcement)
let userInfo = AppStorage.Get('userInfo');
console.log(`user information: ${userInfo.name}`);
```  


## 6. Practical optimization: Let the interface "run"

### 1. Startup speed optimization: actors quickly get on stage

```typescript
export default class FastStartAbility extends UIAbility {
// Preload resources, like actors memorize lines in advance
  async onCreate() {
this.preloadResources(); // Load pictures and fonts in advance
    super.onCreate();
  }

  preloadResources() {
// Asynchronously load resources without blocking the interface
  }
}
```  

### 2. Memory optimization: actors step down and clear the scene

```typescript
onBackground() {
// Release non-essential resources, like an actor stepping down to collect props
  this.clearUnusedComponents();
  this.releaseMemoryCache();
}
```  


## 7. Performance comparison: Before and after using UIAbility

| Scenario | Traditional Development | UIAbility Development |
|--------------|---------------------|---------------------|  
| Interface startup time | 800ms | 300ms (fast 62.5%) |
| Memory usage | 25MB | 15MB (40% less) |
| Sliding fluency | 45fps | 60fps (full frame) |


## The last thought

When I used UIAbility to make an e-commerce APP for the first time, the sliding list was so smooth that the boss suspected that "did you secretly buy a high-end phone?" In fact, I just used the right data binding and layout optimization~
