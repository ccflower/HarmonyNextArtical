# HarmonyOS distributed network advancement: practical optimization of HTTP and network management

> As a developer who is struggling with network request optimization, today I want to share advanced skills of HarmonyOS distributed network!When I was making music apps, I used these methods to increase the data transmission speed by 50%. Now I will share my experience with you~


## 1. HTTP transmission optimization: "Bring a highway" for data express delivery

### 1. "Quit Policy" for Request Methods

HarmonyOS HTTP requests are like sending express delivery, and different methods correspond to different strategies:
- **GET**: Postcard (only data), such as obtaining music list
- **POST**: Package (submit new data), such as uploading playlist reviews
- **PUT/DELETE**: Change address/return file, update or delete server resources

### 2. Music App Practical Practice: 5-step Optimization Request

```typescript
import { http } from '@kit.NetworkKit';

// 1. Create a courier and set up timeout reconnection
let httpRequest = http.createHttp();
let reconnection number = 0;

// 2. Define request parameters (with cache policy)
let reqOption = {
    method: http.RequestMethod.GET,
    header: { 'Content-Type': 'application/json' },
usingCache: true, // Check the local cache first
connectTimeout: 30000, // 30 seconds timeout
    readTimeout: 30000
};

// 3. Initiate a request (with heavy connection logic)
function initiates a request() {
    httpRequest.request(
        'https://api.music.com/playlist',
        reqOption, (err, data) => {
            if (!err) {
console.log('Playlist data is here:', data.result);
                httpRequest.destroy();
} else if (reconnection times < 3) {
Number of reconnections++;
console.log('The express is lost, the second ' + the number of reconnections + 'second reconnections...');
Initiate a request (); // Automatic reconnection
            } else {
console.log('Reconnect failed, prompt the user to check the network');
                httpRequest.destroy();
            }
        }
    );
}

// 4. Start the request
Initiate a request();
```  

### 3. Avoiding pits: Express delivery will not get lost

- **Cache Strategy**: Set `usingCache: true`, cache popular playlists for 1 hour, reduce server pressure
- **Reconnection mechanism**: 3 reconnection + index backoff (1 second, 2 second, 4 second)
- **Error code processing**: 401=Login expires, jump to the login page directly; 503=The server is busy, prompt "try again later"


## 2. Advanced network management: "Smart Navigation" for data traffic

### 1. Multi-network "real-time traffic conditions" monitoring

HarmonyOS's network management is like smart navigation, which can see "road conditions" in real time:
- **Network Quality Score**: Star the network based on latency and bandwidth (1-5 stars)
- **Automatic switching strategy**: 4G < 3 stars when cutting WiFi, WiFi < 2 stars when cutting low power mode

### 2. Network status monitoring practice

```typescript
import { connection } from '@kit.NetworkKit';

// 1. Create a network navigation device
let netNav = connection.createNetConnection({
    netCapabilities: { 
        networkCap: [connection.NetCap.NET_CAPABILITY_INTERNET] 
    }
});

// 2. Subscribe to traffic conditions (with online rating)
netNav.on('netAvailable', (netHandle) => {
// Obtain network quality
    connection.getNetQuality(netHandle.netId).then((quality) => {
let rating = quality.rssi; // Signal strength
console.log('New network available, rating:' + rating + 'star');
        
if (rating < 3) {
console.log('Network poor, enable data compression');
// Enable compressed transmission
        }
    });
});

// 3. Handling when network poor
netNav.on('netUnavailable', () => {
console.log('No internet, cut local cache');
// Show local song list
});
```  

### 3. Optimization of flow "fuel consumption"

| Optimization | Practice | Effect |
|----------------|-------------------------------|---------------------|  
| Data compression | Enable gzip to reduce transmission volume by 60% | Save 30% of traffic in 4G environment |
| Parallel request control | Up to 3 requests at the same time to avoid traffic jams | Improve efficiency but not congestion |
| Weak network mode | When 2G/3G is detected, the picture is reduced to 100kb | Playback does not stutter |


## 3. Practical tips: Don’t get trapped in online requests

- **Merge request**: When obtaining playlist + singer information at the same time, use GET to merge parameters and send one less request
- **Lazy Loading**: First load the cover thumbnail on the list page, click on the details before loading the high-definition picture
- **Breakpoint continues to be transmitted**: Record the progress when downloading large files, and continue from the last location after being disconnected from the network.


## The last thought

I remember when I was making online music for the first time, I didn’t do network optimization and the user feedback was “stuttering songs”.Later, cache and smart switching were added, and songs could be cut in seconds under 4G~ Now users say "as smooth as local playback", which is a very fulfilling feeling!
