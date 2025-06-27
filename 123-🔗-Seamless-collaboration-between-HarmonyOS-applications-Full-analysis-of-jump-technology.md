# 🔗 Seamless collaboration between HarmonyOS applications: Full analysis of jump technology

## 1. Two core jump methods

### 1. Accurate alignment: Pull up the specified application
- **Scene**: Click "View Map" on the Shopping APP to open the map APP directly
- **Features**: Clear goals, no user selection required
- **Implementation**: Through the `openLink` or `startAbility` interface

### 2. Type selection: Pull up the specified type application
- **Scenario**: Click "Transfer" to pop up all bank APP list
- **Features**: The system displays the same type of applications, and users choose independently
- **Implementation**: Combined with type matching through `startAbility`


## 2. Application link: "Digital Bridge" across applications

### Three steps to operate
1. **Target application registration URL skill**
Declare supported URL rules in `config.json`:
   ```json
   {
     "skills": [
       {
         "request": {
           "uri": {
             "scheme": "myapp",
             "host": "shop",
             "path": "/location"
           }
         }
       }
     ]
   }
   ```

2. **Initiator builds the link**
Generate URI according to the target rule: `myapp://shop/location?address=xxx`

3. **System Match Jump**
The system finds the corresponding application based on `scheme/host/path`


## 3. Deep Linking vs App Linking

| Features | Deep Linking | App Linking |
|--------------|--------------------|----------------------|  
| **Protocol** | Custom scheme | Standard https |
| **Domain name verification** | None | Forced verification of domain name legality |
| **Not installed processing**| Return to error code | Jump to browser to open link |
| **Security** | Lower | Higher (HTTPS Encryption) |


## 4. Practical code examples

### 1. Pull up the map application (latitude and longitude jump)
```typescript
import { common } from '@ohos.app.ability.common';

// Pull up the map to display the specified coordinates
context.openLink('geo:39.9042,116.4074'); // Beijing Tiananmen Coordinates
```

### 2. Call the email application (jump with parameters)
```typescript
const want: common.Want = {
  action: 'ohos.want.action.sendToData',
uri: 'mailto:support@harmony.com?subject=feedback&body=problem description',
flags: 3 // Read and write permission flags
};
context.startAbility(want);
```

### 3. In-app Deep Link processing
```typescript
// The receiver handles URI parameters
onCreate(want) {
  const uri = want.uri;
  if (uri) {
    const path = url.URL.parseURL(uri).path;
    if (path === '/product/123') {
      loadProductDetail(123);
    }
  }
}
```  


## V. Development best practices

1. **Not installed processing**
- Check whether the application is installed before jumping:
     `common.getAbilityInfo({ bundleName: 'com.map.app' })`  

2. **Security of Parameters**
- App Linking uses HTTPS protocol to transmit sensitive parameters
- Deep Linking Avoid carrying passwords and other information in URIs

3. **User Experience**
- Provide clear application list description when type jump
- Give clear operation prompts before jumping


## Summarize
HarmonyOS's application jump mechanism achieves seamless collaboration across applications through precise matching and security verification.Developers can choose Deep Linking or App Linking according to the scenario, and combine it with the `openLink/startAbility` interface to create a smooth multi-application interactive experience for users.
