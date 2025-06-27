# Distributed Management Advanced: HarmonyOS makes the device status clear at a glance

> As a developer who has been trapped in distributed development, I found that device status monitoring is a savior!When I was working on a smart home project, I used HarmonyOS's status monitoring function to see the movements of all devices in real time like a clairvoyant eye. Today I will share these practical skills with you~


## 1. Equipment status: "Heartbeat Monitoring" in distributed systems

I remember when I was managing the conference equipment for the first time, I didn’t pay attention to the status of the device. As a result, the speaker’s tablet suddenly went offline and the PPT didn’t pass it over was very awkward!Later I learned that device status monitoring is like installing a "heartbeat monitor" on a distributed system:
- **Real-time dynamics**: For example, the smart light at home suddenly goes offline, and the status monitoring will tell you immediately to avoid looking for switches in the dark
- **Intelligent task scheduling**: When the company's printer is busy, it will automatically send files to other idle devices, as smart as the front desk lady assigns work


## 2. Information query: "Checking household registration" for the device is very simple

### 1. Device information is like "electronic resume"

HarmonyOS's device information query can obtain super detailed device files:
- **Basic Information**: What is the device called (device name), what is it used for (device type), ID number (device ID)
- **Connection status**: Online/offline, network stability or not, it’s like asking a friend, “Are you there? Is the signal good?”

### 2. 5 lines of code search through all devices

```typescript
import { distributedDeviceManager } from '@kit.DistributedServiceKit';

// Create a device manager
let dm = distributedDeviceManager.createDeviceManager('Conference Room Assistant');

// Get all online devices with one click
let device list = dm.getAvailableDeviceListSync();
Device List.forEach((device) => {
console.log(`Devicename: ${Device.deviceName}, online status: ${Device.online? 'Online': 'Offline'}`);
});
```  
This code is like sending a questionnaire to all devices, and the status of the projector in the conference room and colleagues' computers is clear at a glance~


## 3. Status monitoring: the device's "real-time barrage" reminder

### 1. Watch the device dynamics like watching the show starts a barrage

After setting status monitoring, each action of the device will "send a barrage" to notify you:
- When the smart door lock is opened, the monitor will pop up the "device online" reminder
- When the car navigation is offline, it will automatically switch to the hot spot of the mobile phone, which is very considerate~

### 2. Code example: 3 steps to enable "barrage mode"

```typescript
// Assume that the device manager dm has been created
dm.on('deviceStateChange', (state change) => {
// Status change action: online/offline
let action = state change.action === 'online' ? 'Online' : 'Offline';
console.log(`${status change.device.deviceName} just ${action}!`);
    
// Automatically switch tasks when the device is offline
if (!Status Change.online) {
console.log('Transfer tasks to other devices...');
    }
});
```  
This code is like opening a message reminder for the device group. You know everything if you are disconnected and who is online~


## 4. Tips for avoiding pits: FAQs for status monitoring

| Problem Scenarios | Solutions | My Practical Experience |
|------------------|-----------------------------------|-----------------------------|  
| Permission error | Check whether the configuration file has been added | Forgot to add permissions for the first time, I searched for a long time before I found out |
| Device status does not update | Confirm that the device is on the same LAN/Bluetooth | Office partition equipment must be connected with one Wi-Fi |
| Callback did not respond | Check whether the event name is correct ('deviceStateChange') | I once wrote the event name wrong, debugging for half an hour |
