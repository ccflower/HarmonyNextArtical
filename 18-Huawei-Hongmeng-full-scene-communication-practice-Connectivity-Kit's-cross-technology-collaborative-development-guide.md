# Huawei Hongmeng full-scene communication practice: Connectivity Kit's "cross-technology collaborative" development guide

hello!I am Xiao L, the female programmer who "plays with multiple technologies" in the field of Hongmeng Communications~ Have you ever thought about how to synchronize the data of the Bluetooth bracelet to the cloud through WLAN?How to use NFC to trigger P2P direct connection to transfer files?Today, we will unlock the "cross-technical collaboration" capabilities of HarmonyOS Connectivity Kit, and see how Bluetooth, WLAN, and NFC "collaborate seamlessly" to create a full-scene communication experience!


## 1. Technology Panorama: Connectivity Kit's "Three-Dimensional Capacity Matrix"
### (I) Communication Technology Matrix Table
| Technology | Typical Scenarios | Rate | Distance | Power Consumption |
|--------|-----------------------------------|------------|------------|------------|
| Bluetooth | Headphone audio and bracelet data synchronization | 1Mbps | <10m | 0.01-10mW |
| WLAN | 4K screen projection, large file transfer | 100Mbps+ | <100m | 10-1000mW |
| NFC | Touch pairing, bus card simulation | 424kbps | <4cm | Passive mode <1mW |

### (II) Coordinated logic diagram
```mermaid
graph LR
A[User Triggered] --> B{Near Field Interaction?}
B -->|Yes (＜4cm)| C[NFC Quick Start]
B -->|No| D{Require high bandwidth?}
D -->|Yes| E[WLAN Direct Connection/P2P]
D -->|No| F[Bluetooth low-consumption transmission]
C --> G[NFC exchange key/device information]
    G --> E
    G --> F
```


## 2. Cross-technical collaboration: "one-touch connection" scenario implementation
### Scenario: NFC triggers "Bluetooth+WLAN" hybrid connection
#### Step 1: NFC exchange device information (completed within 1 second)
```typescript
import { nfc } from '@kit.ConnectivityKit';

// Listen to NFC tag touch events
nfc.on('tagDetected', (tag) => {
    if (tag.techList.includes('com.nfc.tech.NDEF')) {
        const ndefTag = nfc.getNdefTag(tag);
        const message = ndefTag.readNdefMessage();
const deviceInfo = JSON.parse(message.payload); // parse the device ID, supported technologies, etc.
        startHybridConnection(deviceInfo);
    }
});

// Write to NFC tag (device side)
const ndefMessage = new nfc.NdefMessage([
    new nfc.NdefRecord({
        type: 'application/com.harmony.device',
        payload: JSON.stringify({
            deviceId: 'device-001',
            supportTechs: ['ble', 'wlan_p2p']
        })
    })
]);
nfc.writeNdefMessage(ndefMessage, tag);
```

#### Step 2: Select the connection technology according to the device capabilities
```typescript
async function startHybridConnection(deviceInfo) {
    if (deviceInfo.supportTechs.includes('wlan_p2p')) {
await connectWlanP2p(deviceInfo.deviceId); // Priority for WLAN direct connection to pass large files
    } else if (deviceInfo.supportTechs.includes('ble')) {
await connectBle(deviceInfo.deviceId); // Use BLE to pass small data second-hand
    }
}

// WLAN P2P connection (transfer of pictures/videos)
async function connectWlanP2p(deviceId) {
const config = await nfc.getP2pConfigFromTag(); // Get preconfigured information from NFC tags
    await wifiManager.connectP2pDevice(deviceId, config);
    const socket = await wifiManager.getP2pSocket();
    await socket.sendFile('large-video.mp4');
}

// BLE connection (transmission device status)
async function connectBle(deviceId) {
    const bleDevice = await bluetooth.connect(deviceId);
    const characteristic = await bleDevice.getCharacteristic(uuid);
    await characteristic.startNotify();
}
```


## 3. Intelligent scheduling: "Dynamic Switching" communication strategy
### (I) Bandwidth adaptation: Automatically switch Bluetooth/WLAN
```typescript
// Monitor the transmission rate and automatically switch below the threshold
let transferRate = 0;
setInterval(() => {
if (transferRate < 100 * 1024) { // <100KB/s switch to WLAN
        if (bluetooth.isConnected()) {
            bluetooth.disconnect();
            connectWlanP2p();
        }
} else if (transferRate > 10 * 1024 * 1024) { // >10MB/s keep WLAN
// Maintain the current connection
} else { // Use Bluetooth high-efficiency mode at medium speed
        bluetooth.setTransportMode(bluetooth.TransportMode.HIGH_SPEED);
    }
    transferRate = getCurrentTransferRate();
}, 1000);
```

### (II) Power consumption optimization: Hybrid communication reduces overall energy consumption
```typescript
// BLE transmission control command, WLAN transmission data (combined power consumption is reduced by 60%)
function sendCommandAndData(command, data) {
// Low-power instruction channel (BLE)
    bleCharacteristic.writeValue(command);
    
// High bandwidth data channel (WLAN)
    if (data.size > 1024) {
        wlanSocket.send(data);
    }
}

// Example: Smart camera capture
sendCommandAndData(new Uint8Array([0x01]), cameraFrame); // 0x01=Snap command
```


## 4. Practical cases: Full technical integration of "sports and health" scenarios
### Scene: Bracelet (BLE) + Mobile Phone (WLAN) + Body Fat Scale (NFC) Data Synchronization
#### Step 1: NFC touch fast distribution network (body fat scale → mobile phone)
```typescript
//Write Wi-Fi information on the body fat scale NFC tag
const wifiConfig = {
    ssid: 'HomeWiFi',
    password: 'securepass'
};
nfc.writeTag('nfc://wifi?ssid=${wifiConfig.ssid}&pwd=${wifiConfig.password}');

// The mobile phone reads the NFC tag and connects to Wi-Fi
nfc.on('tagRead', (url) => {
    if (url.startsWith('nfc://wifi')) {
        const { ssid, pwd } = parseUrl(url);
        wifiManager.connectTo(ssid, pwd);
    }
});
```

#### Step 2: BLE real-time synchronized motion data (bracelet → mobile phone)
```typescript
// Band Broadcast Heart Rate Data (BLE)
ble.startAdvertising({
    serviceUuids: ['0000180d-0000-1000-8000-00805f9b34fb'],
    serviceData: [{
        uuid: '00002a37-0000-1000-8000-00805f9b34fb',
        value: new Uint8Array([currentHeartRate])
    }]
});

// Mobile phone parses data and uploads it to the cloud (WLAN)
bluetooth.on('heartRateData', (data) => {
    const heartRate = data[0];
    fetch('https://api.health.com/upload', {
        method: 'POST',
        body: JSON.stringify({ heartRate, timestamp: Date.now() })
    });
});
```

#### Step 3: WLAN batch synchronize historical data (body fat scale → mobile phone)
```typescript
// The body fat scale turns on WLAN AP mode
wifiManager.startAp({ ssid: 'ScaleAP', password: '12345678' });

// Connect and pull data on the phone
wifiManager.connectTo('ScaleAP', '12345678').then(() => {
    const socket = net.createSocket('tcp');
    socket.connect(8080, '192.168.43.1');
    socket.on('data', (chunk) => {
        processHistoricalData(chunk);
    });
});
```


## 5. Performance optimization: "Cross-technology" collaborative scheduling strategy
### (I) Connection pre-establishment: shorten the switching time of multiple technologies
```typescript
// Pre-connect commonly used devices (such as headphones + watches)
Promise.all([
bluetooth.preConnect('headphones-id'), // Pre-connect Bluetooth device
wifiManager.preConnectP2p('watch-id') // Pre-connect WLAN direct connection device
]);

// Use pre-connection channels directly during switching
function switchConnection(tech) {
    if (tech === 'bluetooth') {
        bluetooth.usePreConnected('headphones-id');
    } else if (tech === 'wlan') {
        wifiManager.usePreConnectedP2p('watch-id');
    }
}
```

### (II) Data diversion: allocate communication channels by type
```typescript
const dataRouter = new DataRouter();
dataRouter.route((data) => {
    if (data.type === 'control') {
return 'ble'; // Control command to go to Bluetooth
    } else if (data.type === 'media') {
return 'wlan_p2p'; // Media data is directly connected to WLAN
    } else if (data.type === 'config') {
return 'nfc'; //Configuration information goes to NFC
    }
});

// Use example
dataRouter.send({ type: 'control', payload: command });
dataRouter.send({ type: 'media', payload: videoStream });
```


## 6. Avoiding Pits: "FAQs" of Cross-Technical Cooperation
### (I) Technical conflict: Turn on Bluetooth and WLAN at the same time lead to interference
- **Reason**: 2.4G Bluetooth overlaps 2.4G WLAN band
- *solve*:
    ```typescript
// Priority use of 5G WLAN (if supported)
    if (wifiManager.is5GSupported()) {
        wifiManager.setBand('5GHz');
    }
// Switch to 2M PHY on Bluetooth (reduce interference)
    bluetooth.setPhy(bluetooth.Phy.LE_2M);
    ```

### (II) Coordination logic error: Resources not released in time after NFC is triggered
- **Reason**: NFC tag touch event is not properly unbined
- *solve*:
    ```typescript
    let nfcListenerId;
    function startNfcTrigger() {
        nfcListenerId = nfc.on('tagDetected', handleTag);
    }
    
    function stopNfcTrigger() {
        nfc.off(nfcListenerId);
        nfcListenerId = null;
    }
    ```

### (III) Permission missing: no corresponding permission is applied for cross-technical operations
- **Solution**: Apply for multiple technical permissions at one time
  ```typescript
  featureAbility.requestPermissionsFromUser([
      'ohos.permission.BLUETOOTH',
      'ohos.permission.WIFI',
      'ohos.permission.NFC'
  ], (err, data) => {
      if (data.every(p => p.granted)) {
          initMultiTech();
      }
  });
  ```


## Last chat
The core value of Connectivity Kit lies in breaking the limitations of a single technology. Through the combination of "NFC trigger + Bluetooth control + WLAN transmission", the device can automatically choose the optimal communication method in different scenarios.This "cross-technical collaboration" not only improves the user experience (such as 0.5-second fast connection and inductive switching), but also optimizes device power consumption and performance.In the future, with the addition of UWB ultra-broadband technology, Hongmeng will achieve the integration of "centimeter-level positioning + high-speed communication" and further expand the boundaries of full-scene communication.Next time, let’s talk about “how to use UWB+ Bluetooth to create an “anti-lossing artifact” to achieve accurate tracking of item locations – remember to follow!😉

(Please indicate the source and original author Xiao L when reprinting. Violators... Let your device pop up permission application every time you switch communication technology! Just kidding~)
