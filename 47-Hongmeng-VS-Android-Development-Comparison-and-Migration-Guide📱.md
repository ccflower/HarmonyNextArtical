
#Hongmeng VS Android Development Comparison and Migration Guide📱

Hi~ I am Xiao L!Many Android developers want to transfer to Hongmeng, but they are confused about how big the differences are?Today, use a table + three key points to help you quickly understand the key points of migration~


## 1. Core difference comparison table🚀
| **Dimension** | **HarmonyOS Next** | **Android** |
|------------------|-----------------------------------|---------------------------|  
| **Architecture** | Distributed soft bus, device as a service | Linux hierarchical architecture, mainly single device |
| **UI framework** | ArkUI (declarative, multi-end adaptation for a set of code) | XML+Java/Kotlin (imperative) |
| **Cross-device capability** | Native support (such as distributed task scheduling) | Additional integration framework required |
| **Development Language** | Priority TypeScript/ETS | Java/Kotlin |
| **Permission mechanism** | Minimization principle, dynamic application at runtime | One-time declaration of manifest file |


## 2. Three elements of migration: code, UI, and capability🔧
### (I) Code migration: API "translation" skills
**1. System information acquisition**
```java
// Android: Get device model through Build class
String model = Build.MODEL;  

// Hongmeng: Calling deviceInfo module
import deviceInfo from '@ohos.deviceInfo';  
let model = deviceInfo.getDeviceModel();  
```

**2. Network request**
```java
// Android: OkHttp or HttpURLConnection
OkHttpClient client = new OkHttpClient();  
Request request = new Request.Builder().url(url).build();  

// Hongmeng: @ohos.net.http standard library
import http from '@ohos.net.http';  
let request = http.createHttp();  
request.request({ url: url, method: http.RequestMethod.GET });  
```

### (II) UI migration: from "imperative" to "declarative"
**Case: Button click event**
```xml
<!-- Android XML layout -->
<Button  
    android:id="@+id/btn"  
android:text="click me"
    android:onClick="onButtonClick"/>  

// Corresponding Java code
public void onButtonClick(View view) { /* Logic */ }
```

```typescript
// Hongmeng ArkUI declarative writing method
@Entry  
@Component  
struct ButtonDemo {  
  @State count: number = 0;  

  build() {  
Button('Click Me')
      .onClick(() => {  
this.count++; // Write logic directly in the component
      })  
.text(`Number of clicks: ${this.count}`)
  }  
}  
```

### (III) Capability adaptation: "Plus" for distributed capabilities
**Scenario: Cross-device picture transmission**
```java
// Android: Need to integrate WiFi direct connection or Bluetooth library
BluetoothAdapter adapter = BluetoothAdapter.getDefaultAdapter();  
BluetoothDevice device = adapter.getRemoteDevice(deviceId);  
OutputStream stream = device.createRfcommSocketToServiceRecord(uuid);  

// Hongmeng: One line of code implements distributed transmission
import distributedData from '@ohos.distributedData';  
distributedData.transferFile(deviceId, '/local/pic.jpg', '/remote/pic.jpg');  
```


## 3. A guide to avoid pits: a large inventory of migration "traps"⚠️
1. **Permission Difference**
- Hongmeng needs to declare permissions + runtime dynamic application in `config.json`
   ```json
   {  
     "reqPermissions": [  
       { "name": "ohos.permission.READ_USER_STORAGE" }  
     ]  
   }  
   ```

2. **Thread Model**
- Time-consuming operations are strictly prohibited by the main thread of Hongmeng. `Thread.start()` or `AsyncTask` is required.
   ```typescript
   Thread.start(() => {  
// Backstage tasks
     this.fetchData();  
EventHub.publish('data-loaded'); // Notify the main thread
   });  
   ```

3. **Resource path**
- Hongmeng resource paths are unified to `resources/base/`, and `res/drawable` without Android
   ```typescript
// Load image resources
Image('media:icon') // Directly refer to the file in the media directory
   ```


## 4. Migration tool: Double efficiency tool🚀
1. **DevEco Studio Migration Wizard**
- Supports one-click import of Android projects, automatically identifying migratory codes
- Path: `File > New > Import Project (Android)`

2. **API Comparison Document**
- Hongmeng Developer's official website provides "Android → Hongmeng API comparison table", search for keywords to check replacement solutions

3. **Simulator is turned on more than one **
- Run Android and Hongmeng emulators at the same time to compare the differences in functions in real time


## 5. Summary: Migration "Three Steps"📝
1. **Search**: Use the migration tool to scan the code and mark the Android-specific API that needs to be modified
2. **Replace**: First change the basic functions (such as device information, network requests), and then handle UI and complex logic
3. **Enhanced**: Utilize Hongmeng distributed capabilities (such as multi-device collaboration) to upgrade the application experience
