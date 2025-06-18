# Hongmeng Web Component Debugging: 3 Artifacts from "Catching Blindness" to "Perspective Eye" 🔍

Hi~ I am Xiao L!Debugging the Hongmeng Web components is like "doctors to see a doctor" - using the right tools can quickly find the "cause of the disease".Today I will share 3 magical tools to increase your debugging efficiency by 10 times!


## 1. DevTools: "X-ray machine" for web components
### 1. Turn on debug mode (a key step)
```typescript  
// Turn on debugging when web component initialization
import { Web } from '@ohos.web.webview';  

@Entry  
@Component  
struct MyWebComponent {  
  private controller = new Web.WebviewController();  

  aboutToAppear() {  
// 🔥Key: Allow WebView debugging
    Web.WebviewController.setWebDebuggingAccess(true);  
  }  

  build() {  
    Column() {  
      Web({ src: 'https://example.com', controller: this.controller })  
        .width('100%')  
        .height('100%')  
    }  
  }  
}  
```  

### 2. Connect to debugging tools (3 ways)
| Scenario | Connection Method | Applicable Situation |
|---------------|-----------------------------------|-------------------------|  
| **Embroider** | DevEco Studio → Tools → DevTools | High frequency use during development stage |
| **Real machine debugging** | USB connection device → DevTools automatic identification | Verify real environmental problems |
| **Remote Debugging** | Connect via IP Address → Check "Remote Debugging" | Debugging cross-device collaboration issues |

### 3. Core debugging function (required)
- **Elements panel**:
- View the DOM structure in real time and modify the CSS properties (no need to restart the application)
- Example: Quick positioning CSS rules that lead to misplaced layout

- **Console Panel**:
- Print JS log (`console.log()`)
- Execute temporary JS code (such as calling functions within the page)

- **Network Panel**:
- Monitor HTTP requests and view response data
- Solve FAQs: Interface 404, Data Format Error


## 2. Crashpad: The "black box" of the crash problem
### 1. Enable crash capture (one line of code)
```bash  
# Add permissions in config.json
{  
  "reqPermissions": [  
    { "name": "ohos.permission.READ_LOGS" }  
  ]  
}  
```  

### 2. Crash information analysis process
1. **Get crashed files**:
   ```bash  
# Export .dmp file from device
   hdcd /data/storage/el2/log/crashpad/  
   hdc file pull your_crash_xxxx.dmp ./  
   ```  

2. **Resolve stack information**:
   ```bash  
# Use minidump_stackwalk tool (need to be installed in advance)
   minidump_stackwalk your_crash_xxxx.dmp > stacktrace.txt  
   ```  

3. **Location Problems**:
   ```  
# Typical error example
   ERROR: Failed to load script at line 423  
   Function: renderContent()  
   File: /pages/webview/main.js  
   ```  

### 3. Common causes of crashes and solutions
| Error Type | Cause Analysis | Solution |
|------------------|------------------------------|---------------------------|  
| **JS syntax error** | Undefined variables, brackets mismatch | Check the console panel error |
| **Memory overflow** | Large amount of data processing, unreleased resources | Analysis using Performance panel |
| **WebView version is incompatible** | The device WebView kernel is too old | prompts the user to update the system WebView |


## 3. Performance optimization: "scalpel" for lag problems
### 1. Three axes of performance analysis
- **FPS Monitoring**:
  ```typescript  
// Real-time monitoring of frame rate in JS
  function monitorFPS() {  
    let lastTime = performance.now();  
    requestAnimationFrame(function loop() {  
      const now = performance.now();  
      const fps = Math.round(1000 / (now - lastTime));  
      console.log(`Current FPS: ${fps}`);  
      lastTime = now;  
      requestAnimationFrame(loop);  
    });  
  }  
  ```  

- **Memory snapshot**:
In the Memory panel of DevTools:
1. Click Take Heap Snapshot
2. Compare memory usage at different points in time
3. Look for memory leaks (such as unreleased DOM references)

- **Network Optimization**:
  ```typescript  
// Use Service Worker to cache static resources
  self.addEventListener('fetch', function(event) {  
    event.respondWith(  
      caches.match(event.request)  
        .then(function(response) {  
          return response || fetch(event.request);  
        })  
    );  
  });  
  ```  

### 2. Performance optimization practical cases
**Problem**: Web components load slowly (8 seconds for the first time)
**analyze**:
- Network panel displays CSS loading takes 3 seconds
- Memory panel discovery to create large objects repeatedly

**Solution**:
1. CSS compression: 60% reduction in volume
2. Object pool optimization: multiplexed object instances
3. Lazy loading: non-first screen content is delayed

**Effect**: Loading time is reduced to 2.5 seconds


## 4. Debugging skills are revealed🎯
### 1. Conditional breakpoint (efficiency tool)
```javascript  
// Set condition breakpoint in DevTools
if (user.id === 1001) {  
debugger; // Trigger breakpoint only if user.id is 1001
}  
```  

### 2. Simulate weak network environment
In the DevTools Network panel:
1. Click the Network Throttling drop-down box
2. Select Slow 3G or Custom to customize speed
3. Test the loading logic under weak network

### 3. Custom debugging tools
```typescript  
// Create debug panel components
@Entry  
@Component  
struct DebugPanel {  
  @State isDebugging: boolean = false;  

  build() {  
    Column() {  
      if (this.isDebugging) {  
Text('Debug mode is enabled')
          .backgroundColor('#FF0000')  
          .color('white')  
      }  
Button('Switch debug mode')
        .onClick(() => this.isDebugging = !this.isDebugging)  
    }  
  }  
}  
```  


## 5. Pit avoidance guide ⚠️
1. **WebView version compatible**
- Different devices have big differences in kernel versions
- Suggestion: Detect the WebView version when the application is started and prompt the user to update

2. **Cross-domain issues**
   ```javascript  
// Add cross-domain configuration in web components
   controller.onPageStart = (event) => {  
     controller.setWebviewPreferences({  
       allowFileAccess: true,  
       allowUniversalAccessFromFileURLs: true  
     });  
   }  
   ```  

3. **Memory Leak**
- Avoid creating large numbers of objects in the `onPageFinish` callback
- Storing temporary data using WeakMap


## Summary: The "Golden Rules" of Debugging📝
1. **Observe first**: Use DevTools to analyze the phenomenon, and do not blindly modify the code
2. **Module debugging**: Isolate the scope of the problem, if you first confirm whether it is a JS logic or a CSS problem
3. **Automated testing**: Write unit tests to cover core functions and reduce manual debugging
