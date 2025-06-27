# Unlock HarmonyOS Bluetooth Low Energy: A practical guide to BLE broadcasting and scanning

> As a developer who was anxious about the battery life of smart bracelets, the BLE technology we are going to talk about today is simply a savior of low-power devices!When I was working on a health monitoring project, HarmonyOS's BLE function enabled communication between sensors and mobile phones, and the power consumption was as low as a surprise. Come and take a look at these super practical broadcast scanning techniques~


## 1. BLE: "Power-saving Small Expert" for Low-Power Equipment

When I first came into contact with BLE, I was amazed by its power saving ability - the smartwatch synchronizes data every day and only charges once a week!BLE is the full name of Bluetooth low-power technology, which is especially suitable for this type of "small data volume and long battery life" scenario:
- **Smart Wear**: The bracelet transmits heart rate data to the mobile phone in real time, and does not consume much power
- **Smart Home**: The temperature and humidity sensor is hidden in the corner, and there is no need to change the battery for half a year
- **Medical Equipment**: Blood pressure monitor transmits data through BLE, and can also be used by elderly machines

Take a chestnut🌰: I made a smart flower pot before, used a BLE sensor to monitor soil moisture, and sent data to the mobile phone once a week. A AAA battery lasted for three months, which is a great battery life!


## 2. BLE broadcast: the device's "silent flyer"

### 1. How to post a radio?Get it done in three steps!

Broadcasting is like a device sending a flyer, telling the surroundings "I'm here."It is super simple to implement broadcasting in HarmonyOS:

```typescript
// 1. Turn on Bluetooth first and subscribe to the broadcast status changes
ble.on('advertisingStateChange', (data) => {
console.log('Broadcast state has changed:', data.state);
});

// 2. Set broadcast parameters (interval/power/is connectable)
let setting = {
interval: 160, // Broadcast interval, the larger the value, the more power is,
txPower: 0, // Transmission power, 0 is medium intensity
connectable: true // Allow to be connected
};

// 3. Construct broadcast content (service UUID/manufacturer data)
let advData = {
serviceUuids: ['00001888-...'], // Service ID, like the device's "door number"
    manufactureData: [{
manufacturingId: 4567, // Manufacturer ID
        manufactureValue: new Uint8Array([1,2,3,4]).buffer
    }]
};

// 4. Start broadcasting and get the broadcast ID to facilitate subsequent control
ble.startAdvertising(setting, advData, (err, id) => {
if (!err) console.log('Broadcast starts successfully, ID is', id);
});
```  

### 2. How to use broadcast scenarios?

- **Discovery of the device**: After the smart watch is turned on, broadcast it yourself, and you can match it by scanning the mobile phone
- ** beacon function**: Use BLE to broadcast coupons in shopping malls and stores, and the phone will pop up automatically when it is close to you
- **Sensor announcement**: Smart door lock uses broadcast notification "Someone is opening the door", low power consumption and timely


## 3. BLE scan: Find the "radar" of the device

Scanning is like using radar to find broadcast devices. HarmonyOS can filter specific devices:

```typescript
// 1. Construct the scan filter (only find equipment from specific manufacturers)
let filter = {
manufacturingId: 4567, // Scan only the device of this manufacturer
    manufactureData: new Uint8Array([1,2,3,4]).buffer,
    manufactureDataMask: new Uint8Array([0xFF,0xFF,0xFF,0xFF]).buffer
};

// 2. Set scan parameters (low power mode)
let options = {
interval: 0, // 0 means to use the default interval
dutyMode: ble.ScanDuty.SCAN_MODE_LOW_POWER, // Low power scanning
matchMode: ble.MatchMode.MATCH_MODE_AGGRESSIVE // Aggressive matching, find the device faster
};

// 3. Start scanning, and the callback will be triggered when the device is received
ble.startBLEScan([filter], options);
ble.on('BLEDeviceFind', (devices) => {
    if (devices.length > 0) {
        let deviceId = devices[0].deviceId;
console.log('The device has been found, the ID is', deviceId);
// After finding the device, you can connect and transfer data
    }
});
```  

## 4. Practical skills: Power consumption and efficiency balance

| Operation | Power saving tips | Scenario suggestions |
|------------|-----------------------------------|---------------------------|  
| **Broadcast** | Increase broadcast interval (such as 500ms) | Non-real-time scenarios (sensor reporting) |
| **Scan** | Use filter conditions to reduce invalid scans | Find only specific devices (such as your own door lock) |
| **After connection** | Data is saved and transmitted again, reducing the number of connections | Movement data is transmitted once every 10 seconds |

## The last thought

I remember that when I first adjusted the BLE scan, there was no filtering condition, but I received a bunch of unrelated equipment, and the power consumption increased rapidly.Later, manufacturingId filtering was added, and the power consumption was directly reduced by half!Now when doing projects, you will first think about "how often the broadcast will be sent" and "how big the scanning interval will be set". After all, saving power is the soul of BLE~
