# HarmonyOS distributed hardware management: a super practical guide to keyboard and mouse crossing + peripheral sharing

> As a worker who repeatedly jumps in multi-device office, I finally discovered how delicious HarmonyOS's distributed hardware management is!Last time I was in the meeting, I used a set of keyboard and mouse to control my computer and tablet. My colleague thought I had turned on plug-ins, so today I will share these magical functions with you~


## 1. Keyboard and mouse travel: a set of magic to control all devices

### 1. Application scenario: a magic weapon for meetings?No, it is an efficiency tool!

When the department reported last week, I used the computer keyboard and mouse to directly operate the PPT next to me. The leader thought I had synchronized it in advance~ In fact, HarmonyOS's keyboard and mouse crossing function can allow a set of keyboard and mouse to control multiple devices:
- **Office scene**: When typing on the computer, drag the file directly to the tablet next to it without sending it back
- **Family Entertainment**: When watching TV dramas, using your phone as a keyboard to send barrage is 100 times more convenient than remote control

### 2. Code implementation: 5 lines of code to enable cross-device control

```typescript
import { distributedInputManager } from '@kit.DistributedServiceKit';

// Create a device input manager
let inputMgr = distributedInputManager.createInputManager();
let target device ID = 'colleague tablet deviceId';

// Move the mouse to the (100, 100) coordinates of the target device
inputMgr.sendMouseEvent(target device ID, { type:'move', x: 100, y: 100 });
// Simulate the keyboard to enter "Hello"
inputMgr.sendKeyEvent(target device ID, { type:'down', keyCode: 'H'.charCodeAt(0) });
```  
This code is like installing a portal for the keyboard and mouse. Be careful to complete the equipment authentication first.


## 2. Peripheral sharing: The printer no longer needs to be plugged in

### 1. Shared printer: Money-saving tips for small offices

In the past, every computer in the company connected the printer, and now it uses HarmonyOS to share one:
- **Operation steps**: Computer A connects to the printer, Computer B calls the share directly, and does not need to install the driver
- **Applicable scenario**: Print your child's homework directly at home without turning on the computer to transfer files

### 2. Code example: 3-step sending print task

```typescript
import { distributedPeripheralManager } from '@kit.DistributedServiceKit';

let printer ID = 'living room printer deviceId';
let Print Manager = distributedPeripheralManager.createPeripheralManager();

// Send a print task (assuming the file path is ready)
Print Manager.sendPrintTask(Print ID, { filePath: '/Document/Conference Minutes.docx' })
.then(() => console.log('Print task sent'))
.catch(err => console.error('Print failed:' + err.message));
```  
Remember to confirm that the printer supports distributed sharing first~


## 3. Tips for avoiding pits: I've stepped on these pits for you

| Problem Scenarios | Solutions | My Practical Experience |
|------------------|-----------------------------------|-----------------------------|  
| High keyboard and mouse delay | Together with one Wi-Fi, turn off Bluetooth and try again | It is more stable to use the 5G frequency band when partitioning walls |
| The peripheral cannot be connected | Check whether the device ID is correct and restart the peripheral | The printer is powered off and restarted to solve 80% of the problems |
| Permission error | Add this line to the configuration file: `"ohos.permission.DISTRIBUTED_DEVICE_CONTROL"` | For the first time, I forgot to add permissions, and I searched for half an hour |


## The last thought

When I first traveled with a keyboard and mouse, my colleague saw my mouse flying around between two devices and asked me if I had learned hacking technology. In fact, HarmonyOS packaged the complex distributed protocol very simply. Now my TV, tablet, and printer at home are all connected together, and I can control all devices with a mobile phone~
