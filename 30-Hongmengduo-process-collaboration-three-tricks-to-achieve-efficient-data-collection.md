
# Hongmengduo process collaboration: three tricks to achieve efficient data collection

Hongmeng multi-process communication realizes real-time aggregation of sensor data through process division, lightweight communication and shared memory.This article simplifies the core process and takes you to quickly build a multi-process data acquisition system.


## 1. Process division: collection and integration separation
### 1. Roles and Responsibilities
- **Acquisition process**: Each sensor is independent process and is responsible for real-time data acquisition (such as temperature and humidity, lighting).
- **Integration process**: Summary of all data, process and store it uniformly (such as central control system).

### 2. Communication protocol design
```mermaid
graph TD
Collection process -->|Data frame| Integration process
Data frame format: Magic number (4B) + sensor ID (2B) + data length (2B) + payload (NB) + CRC32 (4B)
```


## 2. Optimization of the collection process: fast collection and fast transmission
### 1. Asynchronous acquisition thread
```cpp
// Non-blocking acquisition (C++ example)
void StartCollecting() {  
    std::thread([this]() {  
        while (isRunning_) {  
int data = ReadSensor(); // Read data
SendData(data); // Asynchronous sending
std::this_thread::sleep_for(10ms); // 10ms interval
        }  
    }).detach();  
}  

void SendData(int data) {  
    OHIPCParcel* parcel = OH_IPCParcel_Create();  
OH_IPCParcel_WriteInt32(parcel, data); // Package data
    OHIPCRemoteProxy_SendRequest(proxy, 0, parcel, nullptr, nullptr);  
    OH_IPCParcel_Destroy(parcel);  
}  
```

### 2. Bulk sending reduces overhead
- Batch 100 pieces of data and send in batches (suitable for high-frequency acquisition).
- Use FlatBuffer to serialize and compress data volume.


## 3. Integration process optimization: stable settlement and stable survival
### 1. Zero copy of shared memory
```cpp
// Initialize shared memory (1MB)
int shmFd = shm_open("/data_shm", O_RDWR | O_CREAT, 0666);  
ftruncate(shmFd, 1024*1024);  
void* mem = mmap(nullptr, 1024*1024, PROT_READ | PROT_WRITE, MAP_SHARED, shmFd, 0);  

// Write data (directly operate memory pointer)
void WriteData(int sensorId, const void* data, size_t size) {  
    std::lock_guard<std::mutex> lock(mutex);  
    char* ptr = (char*)mem + offset;  
    memcpy(ptr, data, size);  
offset = (offset + size) % 1024*1024; // Loop overwrite
}  
```

### 2. Parallel Processing Architecture
```mermaid
graph LR
IPC receiving thread --> Data parsing thread pool --> Business processing thread --> Shared memory write
```


## 4. Process monitoring and exception handling
### 1. Death notification and automatic restart
```cpp
// Register process death monitoring
OHIPCDeathRecipient recipient = {  
    .onRemoteDied = [](void*) {  
        LogWarn("Collector died, restarting...");  
forkAndExec(); // Restart the acquisition process
    }  
};  
OH_IPCRemoteProxy_AddDeathRecipient(proxy, &recipient);  
```

### 2. Elegant exit mechanism
- When receiving an exit signal (such as SIGTERM), the remaining data is sent and then exited.


## 5. Practical optimization and avoiding pits
### 1. Performance indicators
| Indicators | Target Value | Optimization Means |
|--------------|--------------|---------------------------|  
| Single process delay | <10ms | Asynchronous communication + shared memory |
| Number of concurrent processes | 100+ | Thread pool + connection pool |
| Failure Recovery | <500ms | Pre-start backup process |

### 2. Frequently Asked Questions
- **Racing Condition**: Shared memory access lock (such as `std::mutex`).
- **Zombie Process**: Register the `SIGCHLD` signal processing function to clean up the child process.
- **Permissions Issue**: Shared memory requires appropriate permissions (such as `0666`).


## 6. Future trends
- **Dynamic expansion and capacity**: Automatically increase and decrease the acquisition process according to data traffic.
- **Edge Preprocessing**: Collect the process to process locally and upload only feature values.
- **Quantum encryption**: Ensure communication security through quantum keys.


Through the above design, efficient data collaboration between Hongmeng multi-processes can be achieved.The core is to clarify the division of processes, optimize communication efficiency, and do a good job in process monitoring and exception handling.The next article will discuss the application of distributed storage in multi-process scenarios~
