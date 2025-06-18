
# Hongmeng IPCKit: Resource Recycling Guide when Remote Processes Death

Hello everyone!I am Xiao L, focusing on the development of inter-process communications in Hongmeng.When the remote service process suddenly terminates, if effective processing is lacking, the application may face memory leaks, connection residues and other problems.This article will introduce the `DeathRecipient` mechanism of the Hongmeng IPC Kit, and teach you how to elegantly handle resource recycling after the process dies.


## 1. Why do we need to report the process death?
### (I) Risk scenarios
- **Scenario**: The smart home app controls lighting services through IPC. If the device shuts down, the process will die:
- Unhandled: The app continues to send invalid instructions, causing a memory leak or crash.
- **Core problem**: The status of the remote process is not perceived, and local resources cannot be released in time.

### (II) Solution
Hongmeng `DeathRecipient` mechanism:
- Immediately notify the local process when the remote process terminates.
- Provides opportunities for resource recycling, status reset, and reconnection.


## 2. DeathRecipient uses three-step method
### (I) Registration Death Notice
```c
// 1. Create a callback function (process resource recycling)
void OnRemoteDied(void* userData) {
    MyService* service = (MyService*)userData;
    pthread_mutex_lock(&service->lock);
    if (service->proxy) {
OHIPCRemoteProxy_Destroy(service->proxy); // Destroy proxy
        service->proxy = NULL;
    }
    pthread_mutex_unlock(&service->lock);
}

// 2. Bind to the remote proxy
OHIPCDeathRecipient* recipient = OH_IPCDeathRecipient_Create(
OnRemoteDied, myService, NULL // Callback function, user data, release callback
);
OH_IPCRemoteProxy_AddDeathRecipient(proxy, recipient); // Registration Notification

// 3. Log out (when no longer needed)
OH_IPCRemoteProxy_RemoveDeathRecipient(proxy, recipient);
OH_IPCDeathRecipient_Destroy(recipient);
```

### (2) Idepotency protection (anti-repeated recycling)
```c
void OnRemoteDied(void* userData) {
    MyService* service = (MyService*)userData;
    pthread_mutex_lock(&service->lock);
If (service->isDead) return; // Mark has been processed to avoid repeated execution
    service->isDead = true;
// Perform resource recycling...
    pthread_mutex_unlock(&service->lock);
}
```


## 3. Resource management practice: immediately recycling and automatic retry
### (I) List of must-recycled resources
| Resource Type | Recycling Method | Priority |
|----------------|-------------------------|--------|
| Remote proxy object | OHIPCRemoteProxy_Destroy() | ★★★★★ |
| Cross-process handle | OH_IPCObject_Release() | ★★★★☆ |
| Shared memory | shm_unlink() + munmap() | ★★★☆☆ |

### (II) Automatic reconnection mechanism
```c
void OnRemoteDied(void* userData) {
    MyService* service = (MyService*)userData;
    pthread_t timerId;
pthread_create(&timerId, NULL, ReconnectService, service); // Start the reconnect thread
}

void* ReconnectService(void* arg) {
    MyService* service = (MyService*)arg;
sleep(5); // Wait to avoid frequent heavy connections
    service->proxy = GetServiceProxy("com.example.RemoteService");
    if (service->proxy) {
RegisterDeathRecipient(service->proxy, OnRemoteDied, service); // Re-registration notification
    }
    return NULL;
}
```


## 4. Practical case: smart door lock disconnection processing
### (I) Requirements
- When the door lock process dies:
1. Release the proxy object and prompt the user.
2. Automatically try the connection after 5 minutes.

### (II) Key code
```c
typedef struct {
    OHIPCRemoteProxy* proxy;
    pthread_mutex_t lock;
    bool isConnected;
} DoorLockService;

void OnLockDied(void* userData) {
    DoorLockService* service = (DoorLockService*)userData;
    pthread_mutex_lock(&service->lock);
    if (service->isConnected) {
        service->isConnected = false;
OHIPCRemoteProxy_Destroy(service->proxy); // Destroy proxy
ShowToast("Door Lock Offline"); // Notify the user
StartReconnectTimer(service); // Start the reconnect timer
    }
    pthread_mutex_unlock(&service->lock);
}
```

### (III) Effect comparison
| Indicators | Unprocessed | After processing |
|--------------|--------------|--------------|
| Memory Leak | 20KB/Time Disconnection | 0KB |
| Zombie Connection | 5/hour | 0 |
| User Complaints | 12 times/week | 1 time/week |


## 5. Guide to avoid pits
### (I) Avoid callback blocking
**Error**: Perform time-consuming operations (such as file writing, network request) in the callback.
**Correct**: Only lightweight cleaning is done, time-consuming to hand over child threads for tasks.

### (II) Notification registration order
**Error**: First log out the notification and then destroy the agent, notifications may be lost.
```c
OH_IPCRemoteProxy_RemoveDeathRecipient(proxy, recipient); // ❌ Log out first
OHIPCRemoteProxy_Destroy(proxy); 
```
**Correct**: Directly destroy the agent, the system will automatically handle notifications and cancellation.
```c
OHIPCRemoteProxy_Destroy(proxy); // ✅ Automatically remove notifications
```


## 6. Future trends
1. **Pre-death notification**: Server actively sends a "dead" signal to allow the Client to prepare in advance.
2. **Resource Hosting Service**: Similar to Java GC, it automatically tracks cross-process references and destroys idle processes.
3. **Cassociation Recycling of Cross-device**: When the device is offline, the resource release of the associated device is automatically triggered.
