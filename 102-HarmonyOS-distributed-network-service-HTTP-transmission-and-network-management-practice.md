# HarmonyOS distributed network service: HTTP transmission and network management practice

> As a developer who has fallen into network requests, today we must talk about the distributed network capabilities of HarmonyOS!When we were doing weather applications, we used HTTP transmission and network management functions to achieve second-level data updates. Now we share these super practical skills~


## 1. HTTP data transmission: install a "express system" for the device

### 1. The wonderful uses of different "express delivery methods"

HarmonyOS's HTTP supports multiple request methods, just like different express services:
- **GET**: Just check the express delivery progress, just take the data and don’t modify it (such as obtaining weather data)
- **POST**: Like sending express delivery, submitting new data (such as user registration)
- **PUT/DELETE**: Change address/cancel express delivery, update or delete server data

### 2. Weather application practice: Get data in 5 steps

```typescript
import { http } from '@kit.NetworkKit';

// 1. Create courier (HTTP request object)
let httpRequest = http.createHttp();

// 2. Listen to express delivery notifications (subscribe to the response header)
httpRequest.on('headersReceive', (header) => {
console.log('Quit order information:', header);
});

// 3. Send express delivery (initiate a request)
httpRequest.request(
'https://api.weather.com/data', // Express address
    {
method: http.RequestMethod.GET, // Use the method of checking express
        header: { 'Content-Type': 'application/json' },
expectDataType: http.HttpDataType.STRING, // To string express
    }, (err, data) => {
        if (!err) {
console.log('Weather data is here:', data.result);
httpRequest.destroy(); // The courier gets off work
        } else {
console.log('Express is lost:', err);
            httpRequest.destroy();
        }
    }
);
```  

### 3. Guide to avoid pits: Don’t lose express delivery

- **Timeout processing**: Set `connectTimeout: 60000`, and re-issue if the timeout is over
- **Destruction request**: `destroy()` must be adjusted after use, otherwise the courier will occupy the resources
- **Error code check**: 404 is the address is wrong, 500 is the server traffic jam


## 2. Network management: Install "traffic light" on the device traffic

### 1. Multi-network "Traffic Control"

HarmonyOS can manage WiFi, cellular and other networks, such as traffic police to direct traffic:
- **Priority setting**: Video conferencing sets WiFi priority, and 4G is automatically cut without signals
- **Network physical examination**: Real-time measurement of delay and bandwidth, and less data transmission during traffic jams

### 2. Network status monitoring: Real-time viewing of "road conditions"

```typescript
import { connection } from '@kit.NetworkKit';

// 1. Check if there is any path (default network)
connection.hasDefaultNet().then((you net) => {
console.log(There is a network? 'The road is open': 'The road is closed');
});

// 2. Subscribe to traffic conditions
let conn = connection.createNetConnection({
    netCapabilities: { networkCap: [connection.NetCap.NET_CAPABILITY_INTERNET] }
});
conn.on('netAvailable', (data) => {
console.log('New road is open, ID:', data.netId);
// You can start transferring data
});
conn.on('netUnavailable', () => {
console.log('The road is blocked, the data transmission is suspended');
});
```  

### 3. Network "fuel consumption" optimization

| Optimization | Practice | Effect |
|----------------|-------------------------------|---------------------|  
| Data format | Use JSON without XML, express box is lighter | Transmission speed is 30% higher |
| Cache Strategy | Save common data (such as weather icons) | Reduce duplicate requests by 60% |
| Parallel requests | 3-5 requests are sent at the same time, and no road blocking | Overall efficiency is improved by 50% |


## 3. Practical tips: Don’t get trapped in online requests

- **GET vs POST**: Use GET to use data, use POST to post forms, don't reverse it
- **Timeout reconnect**: Set 3 reconnections, each time interval is 1 second, and the user is almost insensitive
- **Waste network processing**: When 4G is detected, the data volume is automatically compressed to 50%


## The last thought

When using HTTP for the first time, the timeout was not processed, and the application was stuck. Later, the reconnection mechanism was added and it was stable. The distributed application we are doing now can automatically switch between WiFi and 4G, and the user said, "It seems like we have a smart brain."
