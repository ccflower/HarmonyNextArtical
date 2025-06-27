# 🏠 HarmonyOS practical combat: Hand in hand with the smart home distributed collaborative system

> As a developer who once turned the smart lights at home to flicker mode and scared Cat, today I want to share the practical smart home battle of HarmonyOS distributed management!From device discovery to permission control, it is all the real things I have covered in the pitfalls~


## 1. The "soul needs" of smart homes

Last week, I helped my neighbor install smart lights and found that what she wants most is:
- **The device is connected in seconds**: When you go home, you can download the mobile phone, and the lights and curtains will be automatically linked.
- **Status synchronization**: Turn off the lights in the living room, and display the status in the bedroom flat panel synchronously
- **Safety Control**: Prevent naughty children from randomly pressing the air conditioning temperature

HarmonyOS's distributed service can handle these things just right, such as installing "walkie radio" for home appliances, and being able to pass on messages to each other without saying anything ~


## 2. System architecture: Give home appliances a "role"

### 1. The division of equipment is very clear

| Device Type | Role Positioning | My Home Example |
|------------|---------------------------|---------------------------|  
| Main control device | Home butler, give orders | My cell phone (scream "I'm back home") |
| Terminal equipment | Executor, follow instructions to work | Living room lights, air conditioners, curtain motors |

### 2. The communication method is smooth only if you choose the right one

- **Multicast Communication**: Like broadcast notifications, suitable for device discovery (mobile phone shouting "Who is here?" Home appliances raise their hands)
- **HTTP request**: Like peer-to-peer chat, suitable for precise control (mobile phone says "turn on warm light" to light)


## 3. Core function implementation: from discovery to control

### 1. Equipment discovery: "Agree" if you shout

```typescript
import { mdns } from '@kit.NetworkKit';

// Create a device discoverer, like shouting with a loudspeaker
let discovery = mdns.createDiscoveryService(this.context, '_smart_light._tcp');

// Write it down when you hear the device response
discovery.on('serviceFound', (device information) => {
console.log(`Finding device: ${device information.serviceName}`);
// The device IP can be stored here for easy subsequent control
});

// Start shouting
discovery.startSearchingMDNS();
```  

### 2. Status synchronization: After turning off the light, the tablet becomes "mirror" in seconds

```typescript
import { http } from '@kit.NetworkKit';

// Send instructions to the lamp (assuming that the lamp IP is 192.168.1.101)
function turn off the lights() {
    http.request(
        'http://192.168.1.101/control',
        {
            method: 'POST',
            extraData: JSON.stringify({ cmd: 'turnOff', brightness: 0 })
        },
        (err, data) => {
            if (!err) {
console.log('Light is off, synchronized to tablet');
// Here is a notification of the tablet's update status
            }
        }
    );
}
```  

### 3. Permission control: Naughty kid can't press the air conditioner

```typescript
// Device authentication image check ID card
function check device permissions (device ID) {
// Check the whitelist of devices from the server here
let whitelist = ['living room light', 'master bedroom air conditioning'];
return whitelist.includes(device ID);
}

// Check the identity first when discovering a new device
discovery.on('serviceFound', (device) => {
if (check device permissions (device.id)) {
console.log('Allow join the home network');
    } else {
console.log('Reject strange devices');
    }
});
```  


## 4. Avoiding pits: The pits that my smart lights have stepped on

- **Network flash break**: The device cannot be connected after the router is restarted?
Solution: Add the logic of disconnection and reconnection, try again every 5 seconds until it is connected

- **Status is out of sync**: The phone turns off, and the tablet is still on?
Solution: Notify all devices at the same time after turning off the lights, such as "broadcast message"

- **Miscontact control**: Turning over and touching the phone in the middle of the night, the light suddenly lit up?
Solution: Add "night mode", control needs to be confirmed after 23 o'clock


## 5. The last thought

Now my equipment can do it: on the way off work, click "I'm going home", the corridor lights are on first, and the living room air conditioner is automatically adjusted by 26℃~ But I remember that during the first debugging, the curtain motor was mistakenly set to "close the door when it opens", and my friend came to visit and was chased by the curtains 😂
