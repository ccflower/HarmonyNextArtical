# 🏭 HarmonyOS Practical Practice: Create a "Classic Eye" distributed monitoring system for industrial equipment

> As a developer who has debugged equipment in the factory until the early morning, today I want to share the actual combat of HarmonyOS in industrial monitoring!When I was using distributed management for equipment monitoring, my master praised it as "three times faster than traditional systems". Come and take a look at these useful things~


## 1. The "life and death demand" of industrial monitoring

Last year, when I helped the workshop change the monitoring system, the master patted the machine and said:
- **Second alarm**: The motor temperature exceeds 80℃ and you must stop immediately. If you slow down for 1 second, the equipment may be burned
- **Discontinued transmission**: Workshop WiFi is occasionally stuck, data cannot be lost
- **Permission Layer**: Workers can only view data, engineers can adjust parameters

HarmonyOS's distributed service is just enough to use, such as installing "intercom + recorder" on each device, and data can be stored when sending messages in real time~


## 2. System architecture: Assign "job types" to the equipment

### 1. The three-layer architecture is super clear

| Levels | Responsibilities | Workshop Examples |
|------------|-------------------------------|---------------------------|  
| Acquisition layer | Hold the sensor thigh and collect the temperature and current data | Temperature sensor on the motor |
| Monitoring layer | Staring at the data and calling someone when you find an abnormality | The monitoring screen of the central control room |
| Control layer | Obey commands and adjust equipment parameters | Frequency converter (control motor speed) |

### 2. Only when the communication plan is selected correctly can it be stable

- **Multicast transmission**: Like workshop broadcast, the motor temperature exceeds the threshold, all workshop equipment knows
- **HTTP long connection**: Like private chat with walkie-talkie, engineers use it when remotely adjusting parameters


## 3. Core function implementation: from monitoring to control

### 1. Equipment Discovery: "Wear a Working Card" for each machine

```typescript
import { mdns } from '@kit.NetworkKit';

// Create equipment scanners, like security guards to check industrial license plates
let scanner = mdns.createDiscoveryService(this.context, '_industry_monitor._tcp');

// Record the information on the work card when discovering the equipment
scanner.on('serviceFound', (device) => {
console.log(`Finding device: ${device.serviceName}, IP: ${device.hostName}`);
// Save the device IP to the "roster"
});

// Start patrol and check equipment
scanner.startSearchingMDNS();
```  

### 2. Data collection: Regularly record data for the device "install diary"

```typescript
import { http } from '@kit.NetworkKit';

// Receive data regularly, like collecting workshop daily reports
function collects data() {
    http.request(
'http://central control room IP:8080/data',
        {
            method: 'POST',
            extraData: JSON.stringify({ 
Motor temperature: 65.2,
Current: 2.3,
Running status: 'Normal'
            })
        },
        (err, data) => {
            if (!err) {
console.log('Data has been reported to the central control room');
            } else {
console.log('Report failed, save the local diary first');
// Save local cache and wait until the network is ready before sending
            }
        }
    );
}

// Receive every 30 seconds
setInterval(receive data, 30000);
```  

### 3. Remote Control: Engineer's "Remote Wrench"

```typescript
// Engineer adjusts the motor speed
function speed adjustment (device IP, new speed) {
    http.request(
`http://${device IP}:8080/control`,
        {
            method: 'POST',
            extraData: JSON.stringify({ 
Command: 'Replace the speed',
Parameters: New speed
            })
        },
        (err, data) => {
            if (!err) {
console.log(`The device has been adjusted to ${new speed}r/min`);
            } else {
console.error('Change speed failed, check permission');
            }
        }
    );
}
```  


## 4. Industrial-grade optimization: pitfalls that have been stepped on in the workshop

- **The disconnected network continues to spread**: There are many metals in the workshop, and WiFi is occasionally interrupted?
Solution: First store the data locally in the device, check the network every 5 minutes, and send it in batches after it is connected, like saving enough daily reports before submitting

- **Permission Layer**: Worker accidentally touched the shutdown button?
Solution: Three levels of permissions—
👀 Worker: Only view data
🔧 Engineer: Can adjust parameters
🔒 Administrator: Only shut down

- **Data encryption**: Are you afraid that the workshop data will be stolen?
Solution: Use AES to encrypt the data during transmission, such as wearing a bulletproof vest for the data, and then decrypt it when the central control room receives it.


## 5. The last thought

Now this system has been running in the workshop for half a year. The most exciting thing is that the motor bearing is about to burn. The system called the alarm 10 seconds in advance, saving the money of a motor~ But I remember that during the first debugging, the inverter parameters were adjusted incorrectly, and the motor suddenly accelerated and scared the master 😂
