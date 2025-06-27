# Teach you step by step on HarmonyOS distributed management: Device discovery and authentication are super easy

> As a developer who has been crashed by device connection, he finally found the way to HarmonyOS distributed management!When I first used it to discover the surrounding equipment, it was like seeing all my friends at a party, which was so fulfilling~ Today I will share these super practical techniques with you~


## 1. Distributed management: Turning the device into a "family"

HarmonyOS's Distributed Service Kit is like a very considerate butler, who can turn mobile phones, tablets, and computers into "a family".I remember the first time I used it, I licked my phone and the tablet in the living room automatically synchronized the conference documents. The feeling of seamless collaboration between devices is so cool!

The core concept is actually very simple: treat all the surrounding equipment as your "clear", such as using a watch to control the smart lights at home, or using a computer to relay the videos you haven't watched by your phone, it's as flexible as your own hands and feet~


## 2. Equipment discovery: You can find "friends" by shouting

### 1. Discovery mechanism is super like the "Find Friends" game

The device finds it like shouting "Who is here" at a party, and the surrounding devices will raise their hands to respond.The DeviceManager interface provided by HarmonyOS allows devices to actively scan "friends" with the same LAN or Bluetooth enabled.When I do the meeting sharing function, I use it to collect all devices in seconds~

### 2. Get the "entry ticket" first and then find someone

You must apply for permission before calling, just like asking for tickets to attend a party.Add a sentence to the configuration file:
```json
{ "requestPermissions": [{ "name": "ohos.permission.DISTRIBUTED_DATASYNC" }] }
```  
This is to protect user privacy. After all, it is not possible to just look at other people's devices~

### 3. Code practice: Find all devices in 5 lines

```typescript
import { distributedDeviceManager } from '@kit.DistributedServiceKit';

// Create a device manager
let dm = distributedDeviceManager.createDeviceManager('My App');

// Start looking for equipment, lasting 2 minutes
dm.startDiscovering({ discoverTargetType: 1 }, { availableStatus: 0 });

// Finding the device will trigger this
dm.on('discoverSuccess', (device information) => {
console.log('The device has been found:', device information.deviceId);
});
```  
This code is like sending a signal to "find friends", which supports filtering equipment by distance and status. It is very convenient to only find equipment in the same conference room during meetings~


## 3. Equipment certification: Send a "trust business card" to the equipment

### 1. Why do you need certification?Prevent "fakes"!

After discovering the device, you have to confirm your identity, which is like exchanging a business card.For example, when a new device is discovered at home smart lock, it must be certified before control is allowed, otherwise it will be dangerous for strangers to open the door~

### 2. Multiple authentication methods are super flexible

HarmonyOS supports "touch", "scan the QR code", "enter the PIN code", etc. I use "touch" authentication for my parents' devices, and I can do it by posting it on my phone. It is 100 times easier than entering a password!

### 3. Code Example: 3 Steps to Build Trust

```typescript
// Assume that the device ID is found is 'device123'
let authentication parameters = {
bindType: 1, // 1 means safety authentication
appName: 'Conference Assistant'
};

dm.bindTarget('device123', authentication parameters, (err, result) => {
    if (err) {
console.error('Certification failed:', err.message);
        return;
    }
console.log('Certification is successful! Now you can collaborate happily~');
});
```  
After authentication, the device will have a "trust tag". You don't need to repeat authentication next time you connect, which is very worry-free~


## 4. Tips for avoiding pits

- **Don't forget to add permissions**: No permissions are like no tickets, you can't do anything
- **The device must be in the same "circle"**: the same LAN or Bluetooth switch, otherwise you will not be able to find it if you scream.
- **Don't send sensitive information randomly**: Device ID and location are all privacy, delete it after use

## The last thought

When I used distributed management for the first time, I looked at my phone and automatically discovered all the smart devices in my home. The sense of control was great!Now when doing projects, we will first plan the equipment discovery and authentication process, and the user said, "The operation is as simple as magic."
