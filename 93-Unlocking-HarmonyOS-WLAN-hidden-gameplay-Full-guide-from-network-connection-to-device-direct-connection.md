# Unlocking HarmonyOS WLAN hidden gameplay: Full guide from network connection to device direct connection

> As a female programmer struggling in the HarmonyOS development pit, today I would like to talk to you about several super practical modes in WLAN development~ Test these skills personally make the interconnection of smart devices as simple as sending notes for best friends. Come and have a look!

## 1. Three "opening methods" of WLAN

HarmonyOS Next's WLAN is like a versatile player. It can switch three modes according to the scene. Let's first highlight you:

- **STA mode**: The most common way to connect to the Internet, such as connecting to the home router with your mobile phone, and relying on the router to take the Internet to fly~
- **P2P mode**: The device directly holds hands and forms a team without the router being an intermediary, and it is super fast to pass files and project screens!
- **AP mode**: Transform yourself into a hot spot, such as turning on a hot spot for your mobile phone to use for tablets, and instantly become a mobile Wi-Fi angel~

Take a sirloin🌰: I watched TV series with my best friend at home on weekends and wanted to cast your mobile phone video on TV. At this time, the P2P mode comes in handy. You don’t have to directly “private chat” with your mobile phone and TV, and the picture is very smooth!

## 2. P2P mode practical combat: direct connection of the device is super simple

### 1. Revealing the applicable scenarios

- **File Quick Transmission**: Pass PPT with colleagues, no longer rely on WeChat to compress the package, directly connect the P2P on two mobile phones, and transfer large files in seconds~
- **Game Black**: When using P2P in multiplayer mobile games, the delay is super low, and you will no longer be afraid of getting stuck in PPT at critical moments!
- **Family Projection**: Travel photos in mobile phone albums, one-click to cast them to smart TVs, which is very convenient to share with your family~

### 2. Three steps to get started with code

First check whether the device supports P2P, the code looks like this:

```typescript
import { wifiManager } from '@ohos.wifiManager';

// See if the device can play P2P~
wifiManager.isP2pSupported().then((supported) => {
    if (supported) {
console.log('The device supports P2P, happy!');
    } else {
console.log('The device does not support it, crying');
    }
});
```  

After confirming support, you can find your friends’ devices:

```typescript
// Turn on P2P mode and search for nearby devices
wifiManager.startP2p().then(() => {
    wifiManager.searchP2pDevices().then((devices) => {
        if (devices.length > 0) {
// Choose a device you want to connect to (such as your best friend’s phone)
            const targetDevice = devices[0];
            wifiManager.connectP2pDevice(targetDevice.deviceId).then(() => {
console.log('Hold hands with my best friend's device successfully!');
            });
        }
    });
});
```  

## 3. Data transmission: as simple as sending WeChat

How to transfer data after the connection is successful?Look at this small example:

```typescript
// Suppose you want to whisper~
const message = 'Come to my house for hot pot!';

wifiManager.getP2pSocket().then((socket) => {
// Sending data is like sending a WeChat message~
    socket.write(message).then(() => {
console.log('The message was sent successfully, wait for my best friend to reply!');
    });
});
```  

Isn't it simpler than expected?In fact, the P2P mode is like sending devices a "private chat license", saving the trouble of Wi-Fi, and is especially suitable for temporary teaming scenarios.When working on a project, I used P2P to achieve synchronous control of multiple smart devices. During the test, my colleagues praised it as "like magic"~

## The last thought

HarmonyOS's WLAN function is like a master key hidden in your pocket. Mastering these three modes can unlock a lot of gameplay during development.I remember when I first started learning P2P, I spent a whole night to adjust the connection. Later I found that the key was actually a few steps, so don’t panic when you encounter problems, just try more~
