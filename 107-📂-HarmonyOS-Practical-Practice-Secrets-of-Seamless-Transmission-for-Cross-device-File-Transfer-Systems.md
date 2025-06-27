# 📂 HarmonyOS Practical Practice: Secrets of "Seamless Transmission" for Cross-device File Transfer Systems

> As a developer who once passed 1GB of demonstration videos in the conference room and crashed, today I want to share the actual combat of HarmonyOS distributed file transfer!When I used the breakpoint continuous transmission function to save me, I almost got killed. Now I have made all these life-saving techniques public.


## 1. "Crash moment" and requirements of file transfer

When I demonstrated the plan to customers last week, the videos passed on to three devices almost failed:
- **Retransmission from the Internet**: WiFi in the conference room suddenly got stuck, and half of the videos were sent back
- **Multiple device sharing**: Mobile phones, tablets, and computers must synchronize the latest version of PPT
- **Permission Control**: You have to restrict editing permissions if you are afraid that customers will delete source files by mistake

HarmonyOS's distributed service is simply a savior. It is like installing a "portal" for files, and it can be transmitted after being disconnected from the Internet~


## 2. System architecture: divide file transfer into "pipeline"

### 1. The four major modules have clear division of labor

| Modules | Responsibilities | Life Analogy |
|---------------|-------------------------------|---------------------------|  
| File discovery | Find equipment, like shouting "Who has the file" in the conference room | Ask colleagues "PPT to send me" |
| Transfer module | Transfer files, send them in blocks, like disassembly express delivery and boxes | Split large files into small portions and deliver them in batches |
| Relay the breakpoint | Record the progress, start from the place where you stopped last time after the internet is cut off | Read the book and start from the fold next time |
| Permission control | Key management, who can see who can change it | Conference room door card, permission to enter |

### 2. Only when the transmission plan is selected correctly will it be faster

- **Multicast transmission**: Like broadcast notifications, transmit the same file block to 3 devices at the same time
- **Block transfer**: Split the large file into 1MB small pieces, and only a small piece is lost when the network is cut off


## 3. Core function implementation: from discovery to continuation

### 1. Equipment discovery: shout "Report" for the file

```typescript
import { mdns } from '@kit.NetworkKit';

// Create a device scanner, like a conference room to call
let fileScanner = mdns.createDiscoveryService(this.context, '_file_share._tcp');

// Write it down when you find the device
fileScanner.on('serviceFound', (device) => {
console.log(`File device found: ${device.serviceName}, IP: ${device.hostName}`);
// Save to the device list to facilitate selection of transmission targets
});

// Start scanning
fileScanner.startSearchingMDNS();
```  

### 2. Multicast transmission: File block "batch airdrop"

```typescript
import { socket } from '@kit.NetworkKit';

// Create a multicast channel, like creating a file transfer group
let multiSocket = socket.constructMulticastSocketInstance();
let multicast address = { address: '239.255.0.1', port: 8888, family: 1 };

// Join the teleportation group
multiSocket.addMembership(multicast address).then(() => {
console.log('Successfully joined to the file transfer group');
    
// Disassemble the file into 1MB chunks
let file block = tear file ('Demo video.mp4', 1024*1024);
File block.forEach((block, number) => {
// Send block, with number to facilitate continuous transmission
        multiSocket.send({ 
data: { number, data: block },
address: multicast address
        });
    });
});
```  

### 3. Breakpoint Continuation: Record "Transfer Progress Notes"

```typescript
// Remember the progress of each file block, like which page to do the homework
let Transfer progress = new Map();

function pass file blocks (file ID, block number, total number of blocks, block data) {
if (block number < total number of blocks) {
// Check if there has been any transmission
if (!Transfer Progress.get(`${File ID}-${Block Number}`)) {
Send block (file ID, block number, block data).then(() => {
// Write it down if the transmission is successful
Transfer progress.set(`${file ID}-${block number}`, true);
Pass file blocks (file ID, block number +1, total number of blocks, block data);
            }).catch(() => {
console.log(`block ${block number} is lost, and it will be retransmitted on the Internet!"
// Write it down if it is not passed, avoid repeated transmission
Transfer progress.set(`${file ID}-${block number}`, false);
            });
        } else {
// The passed block is skipped
Pass file blocks (file ID, block number +1, total number of blocks, block data);
        }
    }
}
```  


## 4. Industrial-grade optimization: pass on files without plague

- **Multi-threaded acceleration**: When transmitting 4K videos, 4 threads are opened and transmitted simultaneously, and the speed is increased by 3 times.
- **Dynamic block size**: WiFi is transmitted by 2MB large pieces, and it will automatically cut 512KB small pieces when it is stuck
- **Encrypted transmission**: Important files are encrypted with AES, such as using password locks to express delivery boxes


## 5. The last thought

Now that you use this system to pass files, you will no longer be afraid of the conference room being disconnected!Last time I passed 3GB of materials to the customer, and the WiFi flashed twice in the middle, and they were automatically uploaded~ But I remember that during the first debugging, the block number was reversed, and the file was spelled out and it was laughed at by my colleagues for a week 😂
