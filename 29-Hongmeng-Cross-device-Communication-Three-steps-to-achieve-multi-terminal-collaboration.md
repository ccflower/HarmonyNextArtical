
#Hongmeng Cross-device Communication: Three steps to achieve multi-terminal collaboration

Hongmeng cross-device communication allows devices such as mobile phones, tablets, watches and other devices to cooperate seamlessly through three steps: "device discovery-connection establishment-data transmission".This article simplifies the core process and allows you to quickly master distributed communication development.


## One and three steps to realize cross-device communication
### 1. Equipment Discovery: Scan the peripheral devices
Call `DistributedDeviceManager` to scan devices that support Hongmeng to obtain device ID (NetworkId) and type.
```java
// Start device scanning (Java example)
DeviceManager deviceManager = DeviceManager.getDeviceManager();  
deviceManager.startDeviceDiscovery(DeviceType.ALL, new DeviceCallback() {  
    @Override  
    public void onDeviceFound(RemoteDevice device) {  
        String networkId = device.getNetworkId();  
        String deviceName = device.getDeviceName();  
// Record device information, such as tablets, watches, etc.
    }  
});  
```

### 2. Establish a connection: Obtain the remote service agent
Obtain the service agent of the remote device through the device ID and establish a communication channel.
```java
// Get the remote service agent (need to know the device ID and service name)
IRemoteObject remoteProxy = DeviceManager.getRemoteDeviceProxy(networkId, "com.example.service");  
if (remoteProxy == null) {  
return; // connection failure handling
}  
```

### 3. Data Transmission: Structured Data Interaction
Use `MessageParcel` to package data, support basic types and serialized objects, and send requests through remote proxy.
```java
// Example of sending data (addition operation)
MessageParcel request = MessageParcel.obtain();  
request.writeInt(5); // Write parameter a
request.writeInt(3); // Write parameter b
MessageParcel reply = MessageParcel.obtain();  
try {  
    remoteProxy.sendRequest(1, request, reply, new MessageOption());  
int result = reply.readInt(); // Get result 8
} catch (RemoteException e) {  
// Handle communication exceptions
} finally {  
    request.reclaim();  
    reply.reclaim();  
}  
```


## 2. Core technology: soft bus and proxy mode
### 1. Soft bus: Automatically adapt to communication protocol
The bottom layer of Hongmeng Soft Bus supports Wi-Fi, Bluetooth and other transmission methods, and automatically selects the optimal channel, so developers do not need to care about the details of the physical layer.

### 2. Agent mode: locally call remote services
The server implements `RemoteObject`, and the client calls the remote method through the proxy object (`IRemoteProxy`), just like calling local code.
```java
// Server side example: Implement remote addition service
public class CalculatorService extends RemoteObject {  
    public CalculatorService() {  
        super("com.example.Calculator");  
    }  
    @Override  
    public boolean onRemoteRequest(int code, MessageParcel data, MessageParcel reply, MessageOption option) {  
if (code == 1) { // Addition request
            int a = data.readInt();  
            int b = data.readInt();  
reply.writeInt(a + b); // Return result
            return true;  
        }  
        return super.onRemoteRequest(code, data, reply, option);  
    }  
}  
```


## 3. Practical combat: Mobile phones and tablets collaborate on editing documents
### 1. Mobile (initiator)
- Discover tablet devices and establish connections
- Transfer document data to the tablet
```java
// Transfer document example
RemoteDevice targetDevice = ...; // Discovered tablet device
IRemoteObject proxy = DeviceManager.getRemoteDeviceProxy(targetDevice.getNetworkId(), "com.example.Editor");  
MessageParcel data = MessageParcel.obtain();  
data.writeString("Document content..."); // Simplify data transmission
proxy.sendRequest(1, data, null, new MessageOption()); // Call the tablet to open the document
```

### 2. Tablet end (server end)
- Receive document data and display it to the editing interface
```java
// Receive the document and display it
@Override  
public boolean onRemoteRequest(int code, MessageParcel data, MessageParcel reply, MessageOption option) {  
    if (code == 1) {  
        String content = data.readString();  
editorView.setText(content); // Update UI
        return true;  
    }  
    return false;  
}  
```


## 4. Optimization and avoiding pits
### 1. Performance optimization
- **Connection pool**: Reuse established connections to reduce duplicate connection overhead
- **Data compression**: Compress before transferring large files (such as using GZIP), reducing traffic consumption
- **Priority Scheduling**: Set high priority in real-time data (such as video) to ensure smooth transmission

### 2. Frequently Asked Questions
- **Disconnection**: Register death notice and clean up failed connections in time
```java
OH_IPCDeathRecipient recipient = OH_IPCDeathRecipient_Create((proxy, userData) -> {  
// Remove the device in the connection pool
}, networkId);  
OH_IPCRemoteProxy_AddDeathRecipient(proxy, recipient);  
```  
- **Permission Configuration**: Declare distributed permissions in `config.json`
```json
"reqPermissions": [  
    "ohos.permission.DISTRIBUTED_DATASYNC",  
    "ohos.permission.DISTRIBUTED_DEVICE_STATE_CHANGE"  
]  
```  
- **Object serialization**: Custom objects need to implement the `Parcelable` interface, or use JSON transmission


## 5. Future trends
- **Invisible Discovery**: Search by function (such as "Print Device"), without manually selecting the device
- **Intelligent routing**: AI automatically selects direct connection or cloud transit to optimize transmission paths
- **Security upgrade**: Combined with quantum encryption, ensure the security of cross-device data transmission


Through the above steps, developers can quickly realize the collaborative functions between Hongmeng devices.The key is to understand the core process of "device discovery-connection-transmission", rationally use the soft bus and proxy mode, and do a good job in performance optimization and exception handling.The next article will explore distributed rendering technology to achieve smooth synchronization of cross-device games~
