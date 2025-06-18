
#Hongmeng Web Component Debugging: 3-trick positioning problems📱

Hi~ I am Xiao L!When debugging Hongmeng Web components, do you always spin in white screen, stuttering, or crashing?Today I will share 3 "artifacts" to quickly find the root cause of the bug and double your efficiency!


## 1. DevTools: Turn on "Perspective Mode" with one click 🔍
### 1. Activate debug permissions (key first step)
```typescript  
// Added during web component initialization
import { Web } from '@ohos.web.webview';  

@Entry  
@Component  
struct WebDebug {  
  private controller = new Web.WebviewController();  

  aboutToAppear() {  
Web.WebviewController.setWebDebuggingAccess(true); // Allow debugging
  }  

  build() {  
    Web({ src: 'https://your-site.com', controller: this.controller })  
      .width('100%')  
      .height('400px');  
  }  
}  
```  

### 2. 3 connection methods (select on demand)
| Scenario | Operation Steps | Advantages |
|--------------|-----------------------------------|-----------------------|  
| **Embroider debugging** | DevEco Studio → Tools → DevTools | Quickly replicate development environment issues |
| **Real machine debugging** | USB connection device → Automatic identification | Positioning real device compatibility issues |
| **Remote Debugging** | Enter the device IP → Check "Remote Debugging" | Debugging distributed applications across networks |

### 3. Three must-learn panels
- **Elements panel**: Real-time modification of DOM/CSS, second-level positioning layout issues
![Elements panel](https://example.com/elements-panel.png)
- **Console panel**: Print logs, execute JS commands, and quickly verify logic
  ```javascript  
console.log('User ID:', userId); // Output key variables
  ```  
- **Network panel**: Monitoring requests time-consuming, responding to data, and solving interface exceptions


## 2. Crashpad: The "black box" of the crash problem📁
### 1. Configure permissions (one line of code)
```json  
// Added in config.json
{  
  "reqPermissions": [  
    { "name": "ohos.permission.READ_LOGS" }  
  ]  
}  
```  

### 2. Analysis process (3-step location crash)
```bash  
# 1. Export the crashed file
hdc file pull /data/storage/el2/log/crashpad/*.dmp ./  

# 2. Parsing the stack
minidump_stackwalk your_crash.dmp > stacktrace.txt  

# 3. View key information
ERROR: Uncaught TypeError: Cannot read property 'xxx' of null  
File: main.js:423 // Locate the specific file line
```  

### 3. Common causes of crashes and countermeasures
| Error phenomenon | Possible cause | Solution |
|----------------|------------------------|---------------------------|  
| White screen | JS syntax error | Check Console panel error |
| Frequent crashes | Memory leaks | Snapshot analysis using Memory panel |
| Device crash | WebView kernel is incompatible | User prompts users to update the system WebView |


## 3. Performance optimization: "scalpel" problem of lag⚙️
### 1. FPS monitoring (JS real-time monitoring)
```typescript  
function checkFPS() {  
  let last = performance.now();  
  requestAnimationFrame(() => {  
    const now = performance.now();  
    const fps = Math.round(1000 / (now - last));  
console.log(`Current frame rate: ${fps} FPS`);
    last = now;  
  });  
}  
checkFPS(); // Start monitoring
```  

### 2. Memory leak troubleshooting (DevTools operation)
1. Open the Memory panel and click "Take Snapshot"
2. After operating the components, take a photo again and compare the differences between the two times.
3. Find unreleased DOM nodes or closure references

### 3. Network optimization practice
**Problem**: Home page takes 8 seconds to load
- **Analysis**: Network displays CSS file 2.5MB, picture not compressed
- **optimization**:
  ```css  
/* Compress CSS volume */
/* Original code */
  .container { padding: 20px 15px; margin: 0 auto; }  
/* After compression */
  .container{padding:20px 15px;margin:0 auto}  
  ```  
  ```html  
<!-- Picture lazy loading -->
  <img src="placeholder.jpg" data-src="real-image.jpg" class="lazy">  
  <script>  
    const lazyImages = document.querySelectorAll('img.lazy');  
    lazyImages.forEach(img => {  
      img.addEventListener('scroll', () => {  
        img.src = img.dataset.src;  
      });  
    });  
  </script>  
  ```  
- **Effect**: Loading time is reduced to 3 seconds


## 4. Quick lookup table for debugging skills📌
| Scenarios | Tips | Code/Operation Examples |
|---------------------|-------------------------------|-----------------------------|  
| Conditional breakpoint | Debug triggers only specific conditions | `if (user.role === 'admin') debugger;` |
| Weak network testing | Simulated 3G network latency | DevTools → Network → Throttling → Slow 3G |
| Custom debugging panel | Real-time display of debugging status | See code example at the end of the article |


## 5. Pit avoidance guide ⚠️
1. **WebView version compatible**
   ```typescript  
// Detect kernel version
   const webviewVersion = Web.WebviewController.getVersion();  
   if (webviewVersion < '12.0.0') {  
showToast('Please update the system WebView');
   }  
   ```  

2. **Cross-domain problem handling**
   ```typescript  
// Configure it during WebView initialization
   controller.setWebviewPreferences({  
     allowUniversalAccessFromFileURLs: true,  
     mixedContentMode: Web.MixedContentMode.COMPATIBILITY  
   });  
   ```  

3. **Memory management taboos**
- ❌ Avoid creating global variables in `onPageFinish`
- ✅ Use `WeakMap` to store temporary references:
     ```javascript  
const elementMap = new WeakMap(); // Automatically recycle non-existent DOM references
     ```  


## Summary: Debugging "Three Steps"
1. **Look at the phenomenon**: Use DevTools panel to locate problem types (layout/logic/performance)
2. **Check log**: Crashpad analyzes crash stack, Console views runtime errors
3. **Optimize**: Starting from the three aspects of network, memory, and rendering to improve the experience
