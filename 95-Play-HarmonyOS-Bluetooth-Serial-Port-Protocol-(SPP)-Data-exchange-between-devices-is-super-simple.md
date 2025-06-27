# Play HarmonyOS Bluetooth Serial Port Protocol (SPP): Data exchange between devices is super simple

> As a developer who often uses Bluetooth to connect sensors and printers, today we must recommend HarmonyOS's SPP protocol!When I was working on a project, I used it to achieve data transmission between mobile phones and hardware devices, which was like building a wireless bridge. Now I will share these super practical skills~

## 1. What is SPP?"Translation Officer" of Equipment Dialogue

When I first came into contact with SPP, I found that it was like a serial translator in the Bluetooth world - it could allow two devices to transmit data like they were connected with data cables.For example, when I was doing a smart home project, the temperature and humidity sensor passed data to the mobile phone through SPP, and it could be synchronized in real time without Wi-Fi, which is especially suitable for scenarios without Internet access.

The core of SPP is to establish Bluetooth serial port connection, common uses include:
- Transfer files to the camera on your mobile phone (save your life when there is no internet outdoors)
- Sensor transmits data to the master device in real time
- Handheld device and Bluetooth printer to make a ticket

## 2 and 3 steps to realize the device's "hand in hand" data transmission

### 1. Server: Set up a "reception station" first

The server must first open a client connection such as Socket, and the code is like this:

```typescript
// Create device monitoring, uuid is the standard identifier of SPP
let serverId = -1;
let sppOption = {
    uuid: '00001101-0000-1000-8000-00805f9b34fb',
secure: true // Enable secure connection
};
socket.sppListen('server1', sppOption, (err, id) => {
    if (err) {
console.error('Supervisor failed:' + err.code);
        return;
    }
    serverId = id;
console.log('Supervising is successful, wait for the client to connect~');
});

// Wait for client connection
let clientId = -1;
socket.sppAccept(serverId, (err, id) => {
    if (err) {
console.error('Client connection failed');
        return;
    }
    clientId = id;
console.log('The client is connected!');
});
```  

### 2. Client: Actively "knock on the door" to connect to the service

The client must know the server address and UUID, for example, when connecting to the printer:

```typescript
//Fill in the Bluetooth address of the target device (a string like the MAC address)
let deviceAddr = '00:11:22:33:44:55';
socket.sppConnect(deviceAddr, {
    uuid: '00001101-0000-1000-8000-00805f9b34fb',
    secure: true
}, (err, socketId) => {
    if (err) {
console.error('Connection failed, check if the address is correct');
        return;
    }
console.log('Connection is successful! Start transferring data~');
    
// Pass a piece of data, such as 'ABCD'
    let data = new Uint8Array([65, 66, 67, 68]);
    socket.sppWrite(socketId, data.buffer);
});
```  

## 3. How to choose SPP and other Bluetooth protocols?

A comparison table has been compiled to facilitate everyone to select the protocol based on the scenario:

| Protocol | SPP | BLE (Bluetooth Low Energy) | A2DP (Audio) |
|--------|--------------|-------------------|---------------------|
| **Suitable for scenes** | File transmission, sensor data | Smart watch synchronization, health equipment | Bluetooth headset listening to songs |
| **Speed** | Medium (faster than BLE) | Slower | Suitable for audio streaming |
| **Power consumption** | Medium (continuous data transmission and electricity consumption) | Super power saving (suitable for battery equipment) | Medium power consumption when listening to music |
| **My Common Scenarios** | Camera photos to mobile phones | Smart bracelets to transmit heart rate data | Wireless headphones to listen to songs |

## The last thought

I remember fiddling with the printer for a long time when I used SPP to connect to the printer, but later I found out that the key is to fill in the UUID correctly (that is the very long string).Now I have SPP testing code on my development board, which is very convenient when debugging hardware~
