
# Hongmeng Web Component Debugging: Efficient Strategy from DevTools to Crash Analysis👨🔧

The debugging of Hongmeng ArkWeb components requires combining system tools and practical skills.This article explains DevTools operations, crash capture and common problem solutions in detail to help you quickly locate and solve problems~


## 1. DevTools: The "Swiss Army Knife" for front-end debugging
### 1. Turn on debug mode
```typescript  
//Enable debug permissions during component lifecycle
@Entry  
@Component  
struct WebComponentDemo {  
// Key code: Allow remote debugging
  aboutToAppear() {  
    webview.WebviewController.setWebDebuggingAccess(true);  
  }  

  build() {  
    Column() {  
      Web({  
        src: 'https://your-domain.com',  
        controller: this.webviewCtrl  
      })  
    }  
  }  
}  
```  

### 2. Core debugging function quick lookup table
| Functional module | Usage description | Operation skills |
|----------------|-----------------------------------|-------------------------------------------|  
| **Elements** | View DOM structure and style | Ctrl+F quickly search for elements, right-click "Edit Attribute" to modify in real time |
| **Console** | Execute JS code/View logs | Enter `$0` to get the currently selected element, `console.trace()` trace function call |
| **Network** | Analyze network requests | Filter `XHR` type to view interface calls, right-click "Copy as cURL" to reproduce the request |
| **Performance**| Performance analysis (frame rate/memory/CPU) | Click "Start Recording" to record the operation process and generate a flame diagram to locate the bottleneck |
| **Sources** | Debugging JS scripts | Refresh the page after setting breakpoints, and monitor variable changes through the "Watch" panel |

### 3. Remote debugging of cross-device scenarios
```mermaid  
graph LR  
A[DevTools] --> B[USB connection to Hongmeng device]
B --> C [Select the target Webview process]
C --> D[Real-time synchronization debugging information]
```  


## 2. crashpad: "detective assistant" for crash problems
### 1. Crash log automatic capture
- **Storage path**: `/data/storage/el2/log/crashpad/*.dmp`
- **File Naming Rules**: `crashpad_${pid}_${timestamp}.dmp`

### 2. Three-step analysis process
```bash  
# 1. Install the toolchain (need to configure the Ninja environment first)
npm install -g crashpad-tools llvm-binaries  

# 2. Parsing the crash stack
minidump_stackwalk /path/to/crash.dmp > stacktrace.txt  

# 3. Positioning the problem code (sample output)
Thread 0:  
0x7f654321 (libarkweb.so!WebCore::HTMLDivElement::click)  
0x7f6543a5 (libarkweb.so!JavaScriptCallStack::execute)  
# Note: 0x7f654321 is a crash address, and it needs to be located in combination with the symbol table.
```  

### 3. Symbol table association skills
```bash  
# Export the application symbol table
hdc file recv /path/to/app/libarkweb.so symbol/  

# Load the symbol table to debugging tool
crashpad-analyze --symbol-dir=symbol/ crash.dmp  
```  


## 3. Frequently Asked Questions and "First Aid Plan"🚑
### 1. White screen problem diagnosis tree
```  
White screen →
├─ Network Problem → Check the DevTools Network panel for 404/500 errors
├─ JS execution exception → Console panel to see if there is an Uncaught Error
├─ Render blocking → Sources panel to see if main.js is loaded synchronously
└─ Style conflict → Elements panel disables CSS rules quickly positioning
```  

**Solution**:
```html  
<!-- Avoid CSS blocking rendering -->
<link rel="stylesheet" href="style.css" media="print" onload="this.media='all'">  
```  

### 2. Stop optimization practice
- **FPS is below 60?**
- Open the Performance panel and view the "Main" thread flame diagram
- Optimize long tasks (>50ms): Disassemble complex calculations into micro tasks
  ```typescript  
// Original code
  function heavyTask() { for (let i=0; i<1e6; i++) {} }  
// After optimization
  function optimizedTask() {  
    setTimeout(() => {  
// Execute in batches
      for (let i=0; i<1e4; i++) {}  
      queueMicrotask(optimizedTask);  
    }, 0);  
  }  
  ```  

- **Memory leak?**
Use the Memory panel for "Heap Snapshot Comparison", filter the "Detached DOM tree" type node, and check whether there are unreleased event listeners.

### 3. Cross-end compatibility issues
| Scenario | Hongmeng Feature Differences | Solution |
|---------------------|-----------------------------|-------------------------------------------|  
| Gesture Events | Support Huawei Pen Pressure Sensing | Add `@ohos.hardware.pen` module compatibility judgment |
| File Selection | Integrated System File Access Framework (FAF) | Use `fileAccess.chooseFile()` to replace native API |
| Multi-window support | Support distributed window management | Create secondary screen window through `windowManager.createWindow()` |


## 4. Efficiency tools and best practices
### 1. Debug panel shortcut keys
| Operation | Shortcut Keys (Windows) | Purpose |
|---------------------|-------------------------|-----------------------|  
| Open DevTools | Ctrl + Shift + I | Call out the debug main panel |
| Switch panel | Ctrl + [ / ] | Switch between panels such as Elements/Console |
| Clear the console | Ctrl + L | Quickly clear the log |
| Screenshot Element | Ctrl + Shift + P → screenshot | Intercept the currently selected element |

### 2. Automated debugging scripts
```javascript  
// Batch verification component style (save as debug.js)
const elements = document.querySelectorAll('.card');  
elements.forEach(el => {  
  if (getComputedStyle(el).color !== 'rgb(51, 51, 51)') {  
console.error(`Component color exception: ${el.id}`);
  }  
});  

// Execute in DevTools Console
load('path/to/debug.js');  
```  

### 3. Log hierarchical management
```typescript  
// Define log level
enum LogLevel {  
  DEBUG = 'DEBUG',  
  INFO = 'INFO',  
  ERROR = 'ERROR'  
}  

// Conditional output (detailed information is displayed only in DEBUG mode)
const debugMode = true;  
function log(message: string, level: LogLevel) {  
  if (level === LogLevel.DEBUG && !debugMode) return;  
  console[level.toLowerCase()](`[${level}] ${message}`);  
}  
```  


## Summary: Debugging the "Four-Dimensional Law"
1. **Tools priority**: Make good use of DevTools real-time monitoring + crashpad offline analysis
2. **Hydrafting Positioning**: Check the problem in the order of "Network→JS→Rendering→Native"
3. **Data Driven**: Use Performance panel data to guide optimization directions
4. **Automation**: Frequently Asked Questions for Bulk Verification of Scripts to Reduce Repeated Operations
