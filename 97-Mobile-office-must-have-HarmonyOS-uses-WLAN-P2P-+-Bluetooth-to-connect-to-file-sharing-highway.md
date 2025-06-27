# Mobile office must-have: HarmonyOS uses WLAN P2P + Bluetooth to connect to file sharing highway

> As a worker who has been busy passing documents countless times in the conference room, he finally discovered the magical combination of HarmonyOS - WLAN P2P and Bluetooth SPP!Last time I used this trick to pass 100MBPPT in seconds, my colleagues asked me if I had turned on a plug-in. Come and see how delicious this wave of operation is~


## 1. Meeting to pass documents?This pair of CPs saved their lives!

Last week at the department’s weekly meeting, the leader suddenly wanted to update the plan, and the equipment of more than 10 people almost broke down.Later I found out that HarmonyOS's "WLAN P2P + Bluetooth SPP" is simply tailor-made for mobile office:
- **WLAN P2P**: Like opening a highway for the equipment, sending large files is very fast, 100MBPPT can be completed in half a minute
- **Bluetooth SPP**: Responsible for passing small notes, such as meeting voting, equipment status synchronization, and low power consumption

Here’s a chestnut: At the meeting now, I will start WLAN P2P and build a temporary shared network. Everyone will connect to upload large files; at the same time, use Bluetooth SPP to synchronize the voting results, both of which are not wrong~


## Set up a file sharing channel in two or three steps

### 1. WLAN P2P to get highway

```typescript
// Check if the device can turn on P2P
wifiManager.isP2pSupported().then((supported) => {
if (support) {
// Turn on P2P mode
        wifiManager.startP2p().then(() => {
console.log('P2P highway has been opened~');
// Search for surrounding equipment
wifiManager.searchP2pDevices().then((device list) => {
if (device list.length > 0) {
// Together with colleagues' equipment
wifiManager.connectP2pDevice(device list[0].deviceId).then(() => {
console.log('The device of my friend has been connected!');
// Use this socket to transfer files
                        wifiManager.getP2pSocket().then((socket) => {
// Write the file transfer code here~
                        });
                    });
                }
            });
        });
    }
});
```  

### 2. Bluetooth SPP pass small paper

```typescript
// Together with colleagues Bluetooth (filter the MAC address of his device)
let device address = '00:11:22:33:44:55';
socket.sppConnect(device address, {
uuid: '00001101-...', // UUID of standard SPP
secure: true // Open a secure connection
}, (err, socketId) => {
    if (!err) {
console.log('Bluetooth small paper strip channel has been established!');
// Pass on the minutes of the meeting (SPP is very suitable for small documents)
let meeting minutes = new Uint8Array([65,66,67]); // "ABC" sample data
socket.sppWrite(socketId, meeting minutes.buffer);
    }
});
```  

## 3. How do you match this CP the most delicious?

| Scenario | Use WLAN P2P | Use Bluetooth SPP |
|--------------|---------------------------|--------------------------|  
| **Transfer large files** | 100MB+PPT/video, transfer in seconds without interruption | Don’t use it!Too slow to doubt life |
| **Transfer small data** | Don’t use it!Too slow to build a connection | Conference voting/message, power consumption is super low |
| **Device synchronization** | Don't use it!Continuous connection and power consumption | Synchronize device status in real time, such as "I'm ready" |

## 4. Practical tips: power-saving and efficient

- **Automatic switching**: Write code to determine file size, >1MB automatically cuts WLAN P2P, <1MB with Bluetooth
- **Time-sharing use**: After passing large files, turn off WLAN P2P and leave Bluetooth SPP for status synchronization
- **Priority connection**: Let everyone join a P2P network before the meeting to avoid temporary equipment search

## The last thought

When I first used this trick, my colleague looked at my phone and clicked a few times and passed the big file. They asked if they had secretly learned hacking technology. In fact, HarmonyOS packaged complex protocols very simply. Now the tablets and laptops in my bag are automatically shared, so I am no longer afraid of being busy in meetings to pass files.
