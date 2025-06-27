# 🔗 Huawei Hongmeng Want Practical Battle: A Complete Guide to the "Magic Bridge" of Component Communication

> As a developer who has been lost in component communication, today I want to reveal the Want of HarmonyOS!When I first used Want to transmit data, my colleague thought I could "transmit things from a distance", but in fact I just mastered the core artifact of this component communication~


## 1. Want: "Courier" between components

### 1. What is Want?

Imagine two stage actors want to preach props:
- Want is the courier in the middle, responsible for packaging and transmitting information
- Can transmit target component address, parameters, data, such as express order + parcel

Core role:
- **Start component**: Tell the system "Which component I want to start"
- **Transfer data**: Start with parameters, like express delivery with additional information
- **Cross-device communication**: Can transmit across devices, such as cross-provincial express delivery


### 2. Express order element (Want attribute)

| Attributes | Functions | Express Analogy |
|--------------|-----------------------|-----------------------|  
| deviceId | Target device ID | Province, city and district address |
| bundleName | Application Package Name | Express Company Name |
| abilityName | Component name | Specific recipient name |
| parameters | Pass parameters | Express parcel content |
| uri | Resource Address | Express Item Link |


## 2. Explicit Want: "Precise Express" for the designated recipient

### 1. Usage scenarios

When you know the specific component name, use explicit Want, such as knowing the recipient's name and address:
- Start Page B from Page A and know the name of B clearly

### 2. Practical code: Start the settings page from the home page

```typescript
import { common, Want } from '@ohos.app.ability';

// Packing express order (explicit Want)
const want: Want = {
bundleName: 'com.example.myapp', // Application package name
abilityName: 'SettingsAbility', // Target component name
  parameters: { 
fromHome: true, // Pass the argument: from the home page
themeColor: 'blue' // Password: theme color
  }
};

// Send express (start component)
common.startAbility(want).then(() => {
console.log('Settings page started successfully');
}).catch((err) => {
console.error('Start failed:', err);
});
```  

### 3. Matching rules: Steps for couriers to find recipients

1. Check whether the bundleName matches (whether the express company is correct)
2. Check whether the abilityName matches (whether the recipient is correct)
3. Check whether the parameters match (whether the package content is consistent)
**Can you sign for all the right ones, otherwise the express delivery will fail**


## 3. Implicit Want: "fuzzy express" to find the recipient by characteristics

### 1. Usage scenarios

If you don’t know the specific component name, please find it according to the function, like “Treasury Department Receives” for express delivery:
- When opening a link, the system will automatically find components that can handle the link

### 2. Practical code: Open the web page link

```typescript
import { common, Want } from '@ohos.app.ability';

// Fuzzy express order (implicit Want)
const want: Want = {
action: 'ohos.want.action.VIEW', // Action: View
uri: 'https://harmonyos.com', // Website
type: 'text/html', // Type: HTML
entities: ['entity.system.browsable'] // Entity: browsable
};

// Send express (start the browser)
common.startAbility(want).then(() => {
console.log('Browser startup successfully');
}).catch((err) => {
console.error('Start failed:', err);
});
```  

### 3. Matching rules: Couriers find people by characteristics

1. Check whether the action matches (whether it can handle the viewing action)
2. Check whether the uri and type match (whether the URL can be processed)
3. Check whether the entities match (whether it is browsable)
**Match result: **
- No match: courier lost
- 1 match: direct delivery
- Multiple matches: let users choose (such as multiple browsers)


## 4. Cross-device Want: "International Express" across provinces

### 1. Cross-device transfer and participation in actual combat

```typescript
import { common, Want } from '@ohos.app.ability';

// Cross-provincial express order (cross-device Want)
const want: Want = {
deviceId: '123456789', // Target device ID
  bundleName: 'com.example.remoteapp', 
  abilityName: 'RemoteAbility',
  parameters: { 
fileData: 'Important Data' // Transfer data across devices
  }
};

// Send cross-provincial express delivery (cross-device startup)
common.startAbility(want).then(() => {
console.log('Remote device component starts successfully');
}).catch((err) => {
console.error('Change-in failed:', err);
});
```  

### 2. Cross-device precautions

- **Discovery of devices**: First discover the device through DistributedDeviceKit
- **Permission Control**: You need to apply for ohos.permission.DISTRIBUTED_DEVICE_INTERACT permission across devices
- **Network requirements**: Need to be on the same LAN or paired devices


## 5. Parameter delivery: "Content secrets" of express parcels

### 1. Basic type delivery

```typescript
const want: Want = {
  parameters: {
userId: 123, // Number
isVIP: true, // Boolean
userName: 'HarmonyUser', // String
userTags: ['dev', 'designer'] // Array
  }
};
```  

### 2. Complex object delivery (requires serialization)

```typescript
// Define user objects
interface User {
  id: number;
  name: string;
  age: number;
}

const user: User = { id: 1, name: 'Zhang San', age: 25 };

const want: Want = {
  parameters: {
userData: JSON.stringify(user) // Serialize object
  }
};

// Receiver analysis
const receivedData = JSON.parse(want.parameters.userData);
```  


## 6. Practical skills: "Guide to avoid pits" for express delivery

### 1. Explicit Want must be checked

- Check whether the bundleName and abilityName are correct (like checking the recipient address)
- Don't write the parameter name incorrectly (if fromHome is written as fromhome, it will not be received)

### 2. Implicit Want optimization

- Use explicit Want first, with higher performance (fuzzy matching fee resources)
- Multiple matching scenarios give users prompts (such as multiple browsers for users to choose)

### 3. Cross-device communication optimization

| Optimization | Practice | Effect |
|----------------|-------------------------------|---------------------|  
| Data compression | Compression before data transmission, like compressed package | Transmission speed +30% |
| Asynchronous processing | Cross-device calls to put asynchronously, like sending registered messages | Do not block the main thread |
| Disconnect the network and reconnect | Reconnect 3 times after failure, such as retransmission of lost items for express delivery | Success rate +90% |


## 7. Interview FAQ: The difference between Want and Intent

| Comparison | Want | Android Intent |
|--------------|-----------------------|-----------------------|  
| Cross-device support | Native support | Extra framework required |
| Parameter type | Support more types | Some types need to be serialized |
| Matching rules | More flexible | More fixed |
| Security | Support for sandbox isolation | Need to be manually processed |


## The last thought

When I used Want to transfer data across devices for the first time, I thought I wanted to write complex protocols~ In fact, HarmonyOS is packaged very simply, as easy as filling out a express delivery form~ Now for distributed applications, cross-device parameters are as smooth as local transmission~
