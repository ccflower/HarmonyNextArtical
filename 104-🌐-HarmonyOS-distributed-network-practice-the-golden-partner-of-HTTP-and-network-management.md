# 🌐 HarmonyOS distributed network practice: the golden partner of HTTP and network management

> As a developer who has stayed up late to debug in network requests, today I want to share with HarmonyOS's "golden partner" - HTTP transmission and network management of the distributed network!When I was working in a shopping app, I used these two functions to load products in seconds. Now I have taken out all the practical skills~


## 1. HTTP transmission: Use a "high-speed express" to the data

### 1. "Express Type" for different request methods

HarmonyOS HTTP requests are like sending express delivery, and different methods correspond to different services:
- **GET**: Postcard (only data collection), such as checking product details
- **POST**: Package (submit new data), such as placing an order to buy something
- **PUT/DELETE**: Change address/return file, update or delete server data

### 2. Practical Shopping App: Get product data in 5 steps

```typescript
import { http } from '@kit.NetworkKit';

// 1. Creation courier (with timeout reconnection)
let httpRequest = http.createHttp();
let reconnection number = 0;

// 2. Define request parameters (with cache)
let reqOption = {
    method: http.RequestMethod.GET,
    header: { 'Content-Type': 'application/json' },
usingCache: true, // First check whether there is a cache locally
connectTimeout: 30000, // 30 seconds timeout
};

// 3. Initiate a request (with heavy connection logic)
function check product (id) {
    httpRequest.request(
        `https://api.shop.com/product/${id}`,
        reqOption, (err, data) => {
            if (!err) {
console.log('Product data is here:', data.result);
                httpRequest.destroy();
} else if (reconnection times < 3) {
Number of reconnections++;
console.log(`${reconnection number}reconnection...`);
Check the product (id); // Automatic reconnection
            } else {
console.log('The network is too bad, display cached data');
                httpRequest.destroy();
            }
        }
    );
}

// 4. Call query
Check the product('12345');
```  

### 3. Guide to avoid pits: Don’t lose items when express delivery

- **Cache Strategy**: Cache popular products for 5 minutes, reducing duplicate requests by 60%
- **Reconnection mechanism**: 3 reconnection + index backoff (1s→2s→4s)
- **Error code processing**: 404=The product is removed from the shelves, and the prompt is directly; 500=The server is busy, try again later


## 2. Network management: Install "intelligent navigation" for traffic

### 1. Multi-network "real-time traffic conditions" monitoring

HarmonyOS's network management like navigation app can:
- **Priority setting**: WiFi is preferred, and 4G is automatically cut without signals
- **Network Score**: Star-to-stars (1-5 stars), and data will be automatically compressed when the network is poor

### 2. Network status monitoring practice

```typescript
import { connection } from '@kit.NetworkKit';

// 1. Create a network navigation device
let netNav = connection.createNetConnection({
    netCapabilities: { 
        networkCap: [connection.NetCap.NET_CAPABILITY_INTERNET] 
    }
});

// 2. Subscribe to traffic conditions (with ratings)
netNav.on('netAvailable', (netHandle) => {
// View the Internet ratings
    connection.getNetQuality(netHandle.netId).then((quality) => {
let rating = quality.rssi; // Signal strength
console.log(`Current network score ${rating}star`);
if (rating < 3) {
console.log('Network poor, image compression 50%');
        }
    });
});

// 3. Handling when there is no internet
netNav.on('netUnavailable', () => {
console.log('Load local cached product');
});
```  

### 3. Traffic "fuel-saving" skills

| Optimization | Practice | Effect |
|----------------|-------------------------------|---------------------|  
| Data compression | Enable gzip to reduce transmission volume by 60% | 4G saves 30% |
| Parallel requests | Up to 3 requests at the same time, no traffic jams | Improved efficiency but no lag |
| Weak network mode | Pictures dropped to 100kb at 2G/3G | Loading speed increased by 2 times |


## 3. Practical skills: Don’t get trapped on the Internet

- **Merge request**: When checking products + comments at the same time, use GET to merge parameters and send one less request
- **Lazy Loading**: First load the thumbnail on the list page, click to load the HD picture
- **Breakpoint continues to be transmitted**: Record the progress of large files, and continue from the last location after the network is disconnected


## The last thought

I remember when I first made the product details page, I didn’t do network optimization and the user feedback was “slow loading”.Later, cache and smart switching were added, and it can be opened in seconds under 4G~ Now the user says "it's as fast as watching locally", which is a very fulfilling feeling!
