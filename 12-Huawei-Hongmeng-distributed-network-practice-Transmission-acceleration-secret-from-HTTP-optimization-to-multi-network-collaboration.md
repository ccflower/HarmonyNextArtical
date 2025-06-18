# Huawei Hongmeng distributed network practice: "Transmission acceleration secret" from HTTP optimization to multi-network collaboration

hello!I am Xiao L, the female programmer who "paved roads and bridges" in Hongmeng distributed network~ Last time we talked about the basic operations of network acceleration, and today we will go deep into the core of distributed management - **network services and data transmission.Do you know?Can Hongmeng’s HTTP requests automatically "select routes" and can also allow WiFi and 5G to work at the same time?Take you to unlock the underlying operation of "double network speed" ~


## 1. HTTP data transmission: more than "sending and receiving data"

### (I) "Eighteen Martial Arts" for Request Methods
| Methods | Scenario Examples | Hongmeng Tenderization Capability |
|--------|-----------------------------------|---------------------------------------|
| **GET** | Get product list, news content | Automatic cache response (`usingCache: true`) |
| **POST** | Submit orders and upload user avatars | Supports chunked transfer of large files (`chunked: true`) |
| **PUT** | Update user information and modify file content | Breakpoint continuous transmission (automatically record transmission progress) |
| **DELETE**| Delete cloud files and log out of user account | Secure deletion (dual verification on server side) |
| **HEAD** | Check whether the file exists and obtain file metadata | Lightweight request (only return to the header to save traffic) |

### (II) Practical combat: Create a "smart request" engine
```typescript
import { http } from '@kit.NetworkKit';

async function intelligent request (url: string, method: http.RequestMethod) {
    const request = http.createHttp();
    try {
// 1. Automatically adapt to network type (WiFi priority to transmit original images, mobile network transmission compressed images)
const net = await Get the current network();
        if (net.type === 'wifi') {
request.header['Accept-Encoding'] = 'none'; // No compression, pass the original image
        } else {
request.header['Accept-Encoding'] = 'gzip'; // Compress, save traffic
        }

// 2. Dynamically set timeout (extended waiting under weak network)
        request.connectTimeout = net.latency > 500? 10000 : 5000;

// 3. Make a request (supports Promise encapsulation)
        const response = await new Promise<http.HttpResponse>((resolve, reject) => {
            request.request(url, { method }, (err, data) => {
                if (err) reject(err);
                else resolve(data);
            });
        });

        return response.result;
    } finally {
request.destroy(); // Ensure resource release
    }
}

// Example of usage: Intelligently obtain user data
Smart request ('https://api.user.com/profile', http.RequestMethod.GET)
.then(data => Render user profile (data))
.catch(err => Display error (err.message));
```

### (III) "Triple protection" for error handling
1. **Retry mechanism**:
   ```typescript
async function request with retry (url, retries = 3) {
       try {
return await smart request (url);
       } catch (err) {
           if (retries > 0) {
await new Promise(resolve => setTimeout(resolve, 1000)); // Wait for 1 second to try again
return Request with retry (url, retries - 1);
           }
throw err; // If the number of retry times exceeds, an error will be thrown
       }
   }
   ```

2. **Fuse breaking mechanism**:
- 5 consecutive requests failed, triggering a fuse, no longer requested the interface within 1 minute
- Dynamically adjust the circuit threshold with distributed configuration center

3. **Downgrade mechanism**:
- Automatically return cached data under weak network, the interface shows "data may not be latest"
   ```typescript
if (err.code === 'NETWORK_ERROR' && has cached data()) {
Show cached data();
       return;
   }
   ```


## 2. Multi-network management: Let the device "good and far"

### (I) Network priority "custom sorting"
```typescript
import { connection } from '@kit.NetworkKit';

// 1. Define network priority policy (WiFi > 5G > 4G > Others)
const priority policy: connection.NetPriority[] = [
    { netCap: connection.NetCap.NET_CAPABILITY_WIFI, priority: 1 },
    { netCap: connection.NetCap.NET_CAPABILITY_5G, priority: 2 },
    { netCap: connection.NetCap.NET_CAPABILITY_4G, priority: 3 },
];

// 2. Apply priority policy
connection.setNetPriority(priority policy);

// 3. Force switch networks (such as using 5G in emergencies)
connection.switchToNet(netIdOf5G);
```

### (II) Multi-network aggregation: "Bandwidth superposition" black technology
#### Scenario: Use WiFi and 5G at the same time when live streaming, and the bandwidth is doubled
```typescript
// 1. Obtain available networks (filter out WiFi and cellular networks)
const nets = await connection.getAllNets();
const candidate network = nets.filter(n =>
    n.type === 'wifi' || n.type === 'cellular'
);

// 2. Start multi-network aggregation (supports simultaneous transmission of up to 3 networks)
const Aggregation ID = connection.startAggregation(candidate network.map(n => n.netId));

// 3. Real-time monitoring of the aggregation status
connection.on('aggregationStateChange', (state) => {
    if (state === 'success') {
console.log('Aggregation succeeds, total bandwidth:', connection.getAggregateBandwidth());
    } else if (state === 'failure') {
connection.stopAggregation(aggregation ID); // Stop aggregation when failure
    }
});

// 4. Stop aggregation (when page is destroyed)
onDestroy() {
connection.stopAggregation(aggregation ID);
}
```

### (III) Network status "second response"
```typescript
// 1. Monitor network capabilities changes (such as sudden VPN permission)
connection.on('netCapabilityChange', (netId, addedCaps, removedCaps) => {
    if (addedCaps.includes(connection.NetCap.NET_CAPABILITY_VPN)) {
Enable encrypted transmission(); // Turn on HTTPS when there is a VPN
    }
});

// 2. Listen to the network disconnection (such as the data network automatically cuts after WiFi is disconnected)
connection.on('netLost', (netId) => {
if (netId === netId of the current WiFi) {
connection.switchToDefaultNet(); // Switch to the default data network
    }
});
```


## 3. Transmission efficiency optimization: "Data slimming" and "path optimization"

### (I) Data compression "load on demand"
#### Client compresses the request body (such as uploading the log)
```typescript
import { compress } from '@kit.CompressionKit';

async function upload compressed log(logData: string) {
// 1. Compress log (gzip compression rate is about 70%)
const compressed data = await compress(logData, 'gzip');
    
// 2. Send compressed data (inform the server to decompress)
    const request = http.createHttp();
    request.header['Content-Encoding'] = 'gzip';
    request.request('https://api.log.com/upload', {
        method: http.RequestMethod.POST,
extraData: compressed data // Send binary compressed data directly
    });
}
```

#### The server returns compressed data (automatically decompressed)
```typescript
// No additional code is required, the Hongmeng HTTP client automatically handles the 'Content-Encoding' header
// When gzip data is received, it will be automatically decompressed to the original content
```

### (II) "Three-level linkage" of cache strategy
1. **Memory Cache**:
- Store high-frequency access data (such as user avatar), valid for 5 minutes
   ```typescript
const memory cache = new Map<string, { data: string, expire: number }>();
function gets memory cache (key: string) {
const entry = memory cache.get(key);
       return entry && entry.expire > Date.now()? entry.data : null;
   }
   ```

2. **Local storage cache**:
- Store offline data (such as news content), valid for 24 hours
   ```typescript
   @Entry
struct local cache {
@State cache data: string = '';
       init() {
this.Cached data = localStorage.getItem('news_cache');
       }
Save cache(data: string) {
           localStorage.setItem('news_cache', data);
       }
   }
   ```

3. **Server Cache**:
- Integrate negotiated caching with HTTP header `Cache-Control`
   ```typescript
   request.header['Cache-Control'] = 'max-age=3600, stale-while-revalidate=60';
   ```

### (III) DNS pre-parsement: "Ask ahead of time" speed up request
```typescript
// Pre-resolve common domain names (such as api.example.com)
connection.preResolveDns('api.example.com');

// Use pre-resolved IP directly when initiating a request to reduce DNS query time
httpRequest.request('http://[preparse IP]/api/data');
```


## 4. Practical case: The underlying implementation of "cross-device file transmission in seconds"

### Scenario: 1GB of video is transmitted between the mobile phone and the tablet, which takes <10 seconds
#### Technical Solution:
1. **Multi-network collaboration**: 5G for mobile phones, WiFi for tablets, and aggregation bandwidth up to 1.2Gbps
2. **Block transmission**: Cut the video into 1MB small pieces and transfer it in parallel
3. **Check the retransmission**: The receiving end checks the integrity of the block, and the missing block automatically requests retransmission

#### Key Code:
```typescript
// Send (mobile phone)
async function Send file (file path: string, receiving device Id: string) {
const file = await file system.readFile(file path);
const block size = 1024 * 1024; // 1MB block
const block number = Math.ceil(file.length/block size);
    
for (let i = 0; i < number of blocks; i++) {
const start = i * block size;
const block data = file.slice(start, Math.min(start + block size, file.length));
        
// Send to multiple networks in parallel (WiFi and 5G)
        await Promise.all([
Send to WiFi (receive device Id, i, block data),
Send to 5G (receive device Id, i, block data)
        ]);
    }
}

// Receiver (Plate)
const Received block = new Set<number>();
function receives block (block number: number, data: Uint8Array) {
Received block.add(block number);
if (received blocks.size === number of blocks) {
Merge files (Array.from(Received Blocks).sort()); // Merge blocks in order
    } else {
// Check for missing blocks and request retransmission
const Missing block = Find missing blocks (received blocks, number of blocks);
if (missing block.length > 0) {
Request to retransmit blocks (missed blocks);
        }
    }
}
```


## 5. Pit avoidance guide: "Training" for distributed networks

### (I) High network switching delay
- **Reason**: The pre-connection is not enabled, and a new connection is created only when switching
- **solve**:
  ```typescript
// Pre-connect to common networks when the application starts
  connection.preConnect('wifi');
  connection.preConnect('cellular');
  ```

### (II) Multi-network aggregation failed
- **Reason**: The system does not support activation of multiple data networks at the same time
- **solve**:
- Check whether the device model is supported (only some flagship phones support it)
- Ensure that the network types are different (such as WiFi+cellular, you cannot connect two WiFi at the same time)

### (III) HTTP request memory leak
- **Phenomenon**: Application memory increases after frequent requests
- **solve**:
- Make sure that `destroy() is called for every request
- Use the request pool to manage request objects:
    ```typescript
const request pool = new Queue<http.HttpRequest>();
function get request() {
return Request pool.length > 0? Request pool.dequeue() : http.createHttp();
    }
function release request (request: http.HttpRequest) {
request.reset(); // Reset request status
request pool.enqueue(request);
    }
    ```


## Last chat
The core of a distributed network is to enable devices to "perceive the environment and dynamically tune".Through functions such as HTTP intelligent request, multi-network aggregation, and data compression, Harmony turns the two major problems of "network delay" and "traffic consumption" into "optimized playground".Next time, let’s talk about “how to use Hongmeng’s P2P transmission to achieve “direct device connection”, such as sending files directly between mobile phones and TVs, without going through the router – remember to follow!😉

(Please indicate the source and original author Xiao L when reprinting. Violators... Let your application frequently disconnect in a multi-net environment! Just kidding~)
