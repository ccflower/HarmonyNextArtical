# HarmonyOS distributed permission control: a practical guide to security and privacy

> As a developer who has been trapped due to permission settings, today I must talk about HarmonyOS's distributed permission control!When I was sharing the device for the first time, I failed to apply for permission correctly, resulting in the function crash. Later, I figured out this set of security mechanisms and now I will share them with you to avoid pitfalls~


## 1. Permission system: "Access Control System" for distributed systems

HarmonyOS's permission system is like community access control, with different permissions corresponding to different door cards:
- **Ordinary permissions**: Look at the device list (analogy for looking at the community bulletin board)
- **Sensitive permissions**: Control equipment, transfer data (analogously use the key to open the door)

I remember the first time I did distributed printing, but I didn’t apply for `DISTRIBUTED_PERIPHERAL_ACCESS` permission. As a result, the printer could not be connected. Later I learned that sensitive operations must have "advanced door card" ~


## 2. Authorization application: Get the "door opening key" in three steps

### 1. Declare the configuration file first

Add permission statement in `module.json5`, like telling the community property what you want to do:
```json
{
  "requestPermissions": [
    {
      "name": "ohos.permission.DISTRIBUTED_DATASYNC",
"reason": "Requires synchronization of device data",
      "usedScene": {
        "abilities": ["MainAbility"],
        "when": "inuse"
      }
    }
  ]
}
```  
`reason` must clearly write why permission is required, otherwise the user may refuse~

### 2. Dynamic application is more considerate

Sensitive permissions must be asked dynamically, such as when binding the device:
```typescript
import { abilityAccessCtrl } from '@kit.AbilityKit';

let atManager = abilityAccessCtrl.createAtManager();
atManager.requestPermissionsFromUser(context, ['ohos.permission.DISTRIBUTED_DEVICE_BIND'])
.then(() => console.log('User authorization succeeds!'))
.catch(() => console.log('User denied permission...'));
```  
It is recommended to apply when the user triggers the operation. For example, when clicking the "Connect Device" button, don't ask when you open the application, as it is easy to be rejected~

### 3. Check permissions and then operate

Before working, check whether you have permission, such as taking out the keys first:
```typescript
import { permissionManager } from '@kit.SecurityKit';

permissionManager.hasPermission('ohos.permission.READ_DEVICE_INFO')
  .then((hasPerm) => {
    if (hasPerm) {
// Perform device information query
    } else {
// Guide the user to set up authorization
    }
  });
```  


## 3. Security strategy: Wear "bodyproof vest" for data

### 1. Data encryption transmission

HarmonyOS will automatically encrypt the transmitted data, such as encrypting a password lock for express parcels.When passing files in actual tests, you can’t see the content with the packet capture tool, which is safe!

### 2. Device authentication like "check ID card"

Supports authentication methods such as touch and input PIN codes. I use "touch and touch" authentication for my parents' devices. I can do it by posting it on my phone. It is 100 times easier than entering a password~

### 3. Operation logs like "surveillance video"

The system will record the device operation log, such as who connected the device, when data is transmitted, and it can trace the source when there is a problem, which is very safe~


## 4. Permissions "door card" for different scenarios

| Scenario | Essential permissions | Application time |
|--------------|-----------------------------------|-------------------------|  
| Device Discovery | `DISTRIBUTED_DEVICE_DISCOVERY` | When clicking "Search for Devices" |
| Equipment Control | `DISTRIBUTED_DEVICE_CONTROL` | When you first click "Control Device" |
| Print Sharing | `DISTRIBUTED_PERIPHERAL_ACCESS` | When selecting a printer |
| Data Synchronization | `DISTRIBUTED_DATASYNC` | When data is first synchronized |


## 5. Tips for avoiding pits: I've stepped on these pits for you

- **What should I do if my permission is denied?**
A pop-up window prompts the user to "set up authorization", and don't let the function be disabled directly, such as: "You have refused permissions, click here to enable it~"

- **The timing of application is very important!**
Don't just want permissions when you open the app. I've tried the home page and asked, and the rejection rate is 90%. Change it to ask when triggering the operation, and the consent rate is increased to 70%.

- **Compare issues with old devices**
Some old devices have different permission mechanisms. Remember to add version to judge, such as:
  ```typescript
  if (getSystemVersion() < 'API 12') {
// Old version processing logic
  }
  ```  


## The last thought

Now for distributed applications, I will draw permission flow charts first, just like planning a community access control route.Last time I used this mechanism to build a home device management app. My parents did not pop up strange permission prompts when using it, which made me feel very accomplished~
