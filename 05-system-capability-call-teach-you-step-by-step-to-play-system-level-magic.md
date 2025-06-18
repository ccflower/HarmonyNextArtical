# system capability call: teach you step by step to play "system-level magic"

hello!I am Xiao L, the female programmer who "digs and digs" in HarmonyOS Next code~ Today we will talk about system capability calls - this is like opening a "system backdoor" for the application, which can directly call Hongmeng's "bottom superpowers".Without further ado, just get some real information!


## Chapter 1: System Ability: Hongmeng’s “Eighteen Martial Arts”

### 1. Core capabilities parade
HarmonyOS Next's system abilities are like superhero groups, each with its own unique skills:
1. **Equipment Management Hero**
- Skills: Real-time monitoring of CPU and memory usage, and can also manage the "work and rest" of the device (such as power saving mode).
- Scenario: When the application is stuck, it can tell you: "Brother, you account for 80% of the memory! It's time to lose weight!"
2. **File System Captain**
- Skills: Local files can be read and written casually, and files can be shared across devices, just like there is a "file portal".
- Leng Knowledge: It has a dedicated "small room" (safety sandbox) for each application. Do other applications want to steal data?No way!
3. **Communication Superman**
- Skills: Can connect to Wi-Fi, Bluetooth, NFC, and use distributed soft bus to make the device "hand in hand".
- Famous scene: Mobile phones and car computers transmit navigation information in seconds through soft buses, which is faster than your object's reply to messages~
4. **Sensor Elf**
- Skill: Can sense temperature, light, acceleration, and even your heartbeat (if the device supports it).
- Usage of brains: Determine whether you are drunk or not according to the degree of shaking you walk, and automatically turn on the "anti-fall mode" ~

### 2. Why is this the "secret cheat" of developers?
Let me give you a chestnut:
- In the past, when developing applications, such as decorating in a "unfinished house", you had to do everything yourself;
- Now with the system capabilities, it is like living in a "fine decoration room", with all water, electricity and coal, just move in with your luggage!
For example, to make a fitness app:
- Use sensor capabilities to directly obtain motion data without connecting to hardware by yourself;
- Use communication capabilities to synchronize data to the cloud in real time, without writing complex network protocols;
- Optimize power consumption with equipment management capabilities, so that the APP can "save power and drive".


## Chapter 2: Document Management: The correct way to "deal with documents"

### 1. Reading files: as simple as dismantling express delivery
```typescript
import fs from '@ohos.file.fs';

async function read file() {
    try {
// Opening a file is like unpacking a express delivery
const file = await fs.open('/data/test.txt', fs.OpenMode.READ_ONLY);
// Create an input stream and start "disconnecting express"
const input stream = await fs.createInputStream(file);
// Prepare the "Express Box" (buffer)
const buffer = new ArrayBuffer(1024);
// Reading data is like taking something from a courier box
const length = await input stream.read(buffer);
const data = new Uint8Array(buffer.slice(0, length));
console.log('File content:' + new TextDecoder().decode(data));
// Remember to "throwing the express box" (release resources)
await input stream.close();
await fs.close(file);
    } catch (error) {
console.error('Read file overturns:' + error.message);
    }
}
```
*Note*: Don't write the path incorrectly!Otherwise, it would be like the express delivery address is written wrongly and the file cannot be found ~

### 2. Write a file: "Write a love letter" to the file
```typescript
import fs from '@ohos.file.fs';

async function write file() {
    try {
// Open the file and create it if it doesn’t exist (like buying a new notebook)
const file = await fs.open('/data/test.txt', fs.OpenMode.CREATE | fs.OpenMode.WRITE);
// Create an output stream and prepare to "start a pen"
const output stream = await fs.createOutputStream(file);
// Writing data is like writing words on a notebook
const content = new TextEncoder().encode('This is Xiao L's code notes~');
await output stream.write(content);
console.log('Written successfully, the file now has words!');
// Close the notebook (close the stream)
await output stream.close();
await fs.close(file);
    } catch (error) {
console.error('Failed to write file:' + error.message);
    }
}
```
*Hearing joke*: If the write fails, it may be that the file is "inflicting a temper" (no permissions), remember to give it a "Permissions Cake" ~


## Chapter 3: Advanced Operation: Let the sensor and network "group CP"

### 1. Intelligent environment monitoring: Make the device "clear ears and bright eyes"
Imagine: your smart air conditioner can adjust the temperature by yourself and transmit data to your mobile phone - this is the "dream linkage" of sensor + network!

#### 1. Step 1: Use sensors to "sensor the world"
```typescript
import sensor from '@ohos.sensor';

function temperature measurement() {
const Temperature sensor = sensor.getDefaultSensor(sensor.SensorTypeId.TEMPERATURE);
if (temperature sensor) {
// Subscribe to temperature changes, just like monitoring the air conditioner remote control
Temperature sensor.subscribe((data) => {
console.log(`Current temperature: ${data.temperature}℃, a little ${data.temperature > 26 ? 'hot' : 'cold'}~`);
        });
    } else {
console.log('The temperature sensor cannot be found, the device may have "cold"~');
    }
}
```

#### 2. Step 2: Use the Internet to "transmit intelligence"
```typescript
import http from '@ohos.net.http';

async function transfers data to the cloud (temperature: number) {
const request = http.createHttp();
const option = {
        method: http.RequestMethod.POST,
url: 'http://your cloud address/api/temp',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ 
Device ID: 'SmartAirConditioner_001',
Temperature: Temperature,
Time: new Date().toISOString()
        })
    };
    try {
const response = await request.request(option);
console.log('The cloud has received temperature information, reply:' + response.result);
    } catch (error) {
console.error('Information delivery failed:' + error.message);
    } finally {
Request.destroy(); // Destroy the request and save resources
    }
}
```

#### 3. Step 3: Group CP!Let the data "move"
```typescript
Temperature measurement(); // Start measuring temperature first
setInterval(async () => {
const Temperature sensor = sensor.getDefaultSensor(sensor.SensorTypeId.TEMPERATURE);
if (temperature sensor) {
const latest data = await temperature sensor.read(); // Actively read data once
await transfers data to the cloud (latest data.temperature); // transfers to the cloud
    }
}, 60 * 1000); // Transfer data once a minute
```
*Scene Expansion*: Change the temperature to heart rate and you can do health monitoring; change it to light intensity and you can automatically adjust the screen brightness - only you can't imagine, but you can't do it without the system's ability!


## Chapter 4: The guide to avoid pits: "Unfacing Rules" for Calling System Capabilities
1. **Permission number one**:
Call sensors, network and other capabilities, declare permissions in config.json first, otherwise you will be "rejected" by the system~
   ```json
   {
     "reqPermissions": [
       {
         "name": "ohos.permission.GET_SENSOR_DATA"
       },
       {
         "name": "ohos.permission.INTERNET"
       }
     ]
   }
   ```
2. **Async processing**:
Most system capability calls are asynchronous. Remember to use `async/await` or `.then()` to not let the code "stuck" ~
3. **Resource Recycling**:
Open file streams and subscribed sensors, remember to close them after using them!Otherwise, the memory will be like a "leaked bucket", and the more you use it, the less you use it~


## Last chat
System capability calling is like learning to drive: at first I feel complicated (remember various APIs), but after being proficient, you can "storm all the way" ~ Next time I will tell you how to use device management capabilities to "application performance optimization", and by the way, "how to make the APP as lightweight and power-saving as a cat", remember to follow!😉

(Please indicate the source and original author Xiao L when reprinting. Violators... Let your code report permission errors every day! Haha~)
