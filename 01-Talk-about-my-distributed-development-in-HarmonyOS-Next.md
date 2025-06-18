# Talk about my distributed development in HarmonyOS Next

Hello everyone!I am Xiao L. I have recently plunged into the distributed development of HarmonyOS Next. I have also accumulated some experience after trampling on pitfalls. Today I will talk to my sisters about this. Let me make a statement first. This article is based on practical summary. It is purely hand-made and useful. If you have anything wrong, please welcome friends in the technical circle to take pictures at any time. If you reprint it, remember to bring the original author and source~

## Chapter 1: What is distributed?

### 1. The secret of underlying logic
HarmonyOS Next's distributed capabilities rely entirely on the "soft bus" to support the field. This thing is like an invisible rubber band, "bounces" together mobile phones, tablets, and speakers.To put it bluntly, it is to put a layer of "virtual skin" on hardware resources. When writing applications, we don't have to care whether the opposite side is a mobile phone or a TV. Just call the other party's abilities directly, and it's as easy as writing code on your own device~

There are three tough characters in the architecture:
- ** Device Management Module**: Responsible for finding devices all over the world, saying hello to the neighbor's smart speaker, "I'm a mobile phone, can I connect to you?" After confirming your eyes, you will build trust.
- **Task Scheduling Module**: Assigning work like a workshop director, when you see your phone is busy watching TV series, you throw the calculation task to the tablet next to you: "You have good performance, leave this job to you!"
- **Data Management Module**: When data jumps between devices, it holds the small flag of "consistency" to supervise, ensuring that the phone changes the document, and the version on the tablet is immediately synchronized, and the embarrassing scene of "you have it, I don't" will never be allowed.

### 2. Wonderful applications in life
Let’s give two down-to-earth examples:
1. **Smart Home Lazy Mode**
I took out my cell phone on the way off work, and sent a "instruction package" to my home with a slight click: I first recognize who I am, the lamp will automatically turn to warm yellow, the air conditioner will quickly turn the temperature to 24 degrees, and the TV will consciously turn on the TV. I don't have to switch N APPs throughout the process, just like an invisible butler helps me get everything done~
2. **The worker's fishing artifact**
Would you like to lie down when you write a plan on your mobile phone?Just "throw" the document onto the tablet and continue to knock it. The computer secretly opens the slutty page and does not delay real-time synchronous modification.Even more amazing when meetings, the PPT on the phone can "fly" to the big screen. I secretly replied to messages on my phone while taking notes on my tablet (shhhhh, don't tell the boss). The task scheduling system clearly divides the work, and is so efficient that it is flying~

## Chapter 2: Development Practical Practice: From Code to Implementation

### 1. Task Scheduling: Let the devices roll up!
1. **Please tricks to pick equipment**
The system is like a smart HR. When choosing a device for a task, you have to read the "resume": a strong CPU, a heavy job, a large memory, and a good network is responsible for passing files.For example, when dealing with 4K video, you will definitely give priority to placing tasks on your home desktop computer, and you will be the remote control.
2. **Code practice: Let the device group arithmetic problem**
```typescript
// Import the dispatch module first, just like hiring a temporary worker
import distributedTask from '@ohos.distributedTask';

// Write a function that calculates the sum, which is so simple that elementary school students can
function calculateTask(data: number[]): number {
    let result = 0;
    data.forEach(num => result += num);
    return result;
}

async function teaming sum () {
    try {
// Let's first check which equipment is online, like checking the attendance sheet
const devices = await distributedTask.getDeviceInfos();
if (device.length === 0) {
console.log('Good guy, I'm just a bare commander');
            return;
        }
// Cut the data into small pieces and distribute it to each device
const original data = [1, 2, 3, ..., 100]; // I am too lazy to write the whole thing, use... instead
const How much is divided per device = Math.ceil(raw data.length / devices.length);
const subtasks = [];
Devices.forEach((_, i) => {
const start = i * how many points per device;
const end = Math.min(start + how much is divided per device, original data.length);
Subtasks.push(raw data.slice(start, end));
        });
// It's time to send a job!Each subtask corresponds to a device
const task order number = await distributedTask.submitTasks(calculateTask, subtasks, devices);
// Waiting for the result is like waiting for takeaway, but here is waiting for the calculation result
const Results = await distributedTask.getTaskResults(task order number);
const final result = results.reduce((acc, cur) => acc + cur, 0);
console.log(` was finally calculated! The result is: ${final result}`); // It should be 5050, right?
    } catch (error) {
console.error(`Success! Error: ${error.message}`);
    }
}
```
This code is like opening a "math cram school" for the devices. Each device calculates part of it, and finally collects the answers and matches them. The efficiency is much faster than doing it alone.

### 2. Data sharing: Let the devices chat
1. **Synchronous Magic: Data runs by itself**
When the counter on the phone is increased by 1, the numbers on the tablet and watch also jump, just like three devices playing "You draw me, guess", and one moves and the other two immediately follow.It depends on the data management module as a "sound tube" behind the scenes. If there is any change, just shout it quickly~
2. **Code Demonstration: Make a counter that can cross devices**
```typescript
import distributedData from '@ohos.distributedData';

async function cross-device counter() {
    try {
// Build a "data house" first, called counter, allowing automatic synchronization
const house building option = {
uri: 'counter://our nest/',
createIfMissing: true, // Create new ones if not
autoSync: true, // The automatic synchronization switch is turned on
kvStoreType: distributedData.KVStoreType.SINGLE_VERSION // Just a simple version
        };
const Data Manager = await distributedData.getKVManager();
const counter warehouse = await Data Manager.getKVStore (house building option);
// Let's see how much you have now
const old value = await counter repository.get('counter');
const new value = (old value ? parseInt(oldValue) : 0) + 1;
await counter repository.put('counter', newValue.toString());
console.log(`I have calculated ${newValue} here!`);
// Listen to other devices, like eavesdrop
Counter repository.on('dataChanged', (event) => {
console.log(`The counter has been changed with the device! Now it is ${event.value}`);
        });
    } catch (error) {
console.error(`Data synchronization overturns: ${error.message}`);
    }
}
```
Now, no matter whether you click the plus sign on your phone or change the number on your tablet, all devices will instantly display the latest value and you will no longer need to synchronize manually~

## Chapter 3: Practical Case: I built a cross-device music player

### 1. Full record of functional development
The player I made is very good: the mobile phone is used as a "remote control", the speaker is used as a "speaker", and the tablet is used as a "lyrics book".How did it be achieved specifically?
- **Task dispatcher helps**: The mobile phone is responsible for "grabing" music files from the cloud, and then packing and sending the "play command" to the speaker: "This song is called "Code Love Song", play it quickly!" The speaker starts working when it receives work, and the mobile phone takes the opportunity to be lazy~
- **Data Sharing Show Operation**: Playback progress and volume are like running around between devices with long legs.I turned the volume on the tablet, and the volume bars on the speaker and the phone also moved, just like three devices dancing hand in hand~

### 2. Guide to pits and filling pits
1. ** Equipment compatibility battle**
Some speakers only know MP3, while others only understand FLAC. What should I do?I added a "translation officer" (audio conversion module) to the code, first ask the speaker "What format can you understand?", and then convert the music file into the corresponding format and send it to it, which will solve the problem perfectly~
2. **Network fluctuation first aid kit**
Once, the WiFi at home suddenly got fucked, and the music was stuck like PPT.I added a "buffered warehouse" to the player. When the network is good, store more data, and when the network is bad, take it from the warehouse. It is like stocking up good snacks in advance, and don't panic when you are hungry~ I also added a "network monitor" and paused when the network is off. When it is OK, it will automatically continue broadcasting, and no longer need to be manually operated~

When I wrote this article, I suddenly sighed that the distributed development of HarmonyOS Next is like building blocks, putting together the abilities of different devices and making a lot of tricks. Of course, there must be pitfalls on the road, but we programmers just rely on filling in pitfalls to upgrade!I hope my experience can help the newly-entered sisters, let’s ride the wind and waves in the ocean of code together~ If you have any questions, please click on me at any time. See you in the comment section!💻✨
