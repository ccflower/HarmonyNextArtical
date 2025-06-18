# Hongmeng Next application continuous development: a guide to seamless relay for multi-device

The application continues to enable seamless task migration when users switch applications between mobile phones/tablets/cars.This article analyzes the core API and practical processes to help you create a cross-device collaborative experience~


## 1. Continuing capability: "Task Relay Race" for multiple equipment
### Core Concept
- **Distributed State Synchronization**: Synchronize application data and interface status through soft bus
- **Migrate three elements**: Save data from source equipment → Transfer → Recovery of target equipment

### Typical Scenario
| Scene | Continuation effect |
|---------------------|-----------------------------------|  
| Mobile phone → Tablet reading | Browser page scrolling position and bookmark synchronization |
| PC→Car navigation | Seamless migration of map routes and destination parameters |
| Tablet → Watch To-Do | Real-time synchronization of task progress and reminder settings |


## 2. Practical analysis of core APIs
### 1. Source data preparation: onContinue()
```typescript  
export default class SourceAbility extends UIAbility {  
  onContinue(wantParam: Record<string, any>) {  
// 1. Version compatibility verification
    const targetVersion = wantParam.version || 0;  
    if (targetVersion < 2.0) {  
return AbilityConstant.OnContinueResult.MISMATCH; // Version is incompatible
    }  
// 2. Package migration data (text, progress, etc.)
    wantParam['editorContent'] = this.currentText;  
    wantParam['scrollPos'] = this.scrollY;  
    return AbilityConstant.OnContinueResult.AGREE;  
  }  
}  
```  

### 2. Target side recovery: onCreate()
```typescript  
export default class TargetAbility extends UIAbility {  
  onCreate(want: Want, launchParam: any) {  
    if (launchParam.launchReason === 'CONTINUATION') {  
// 1. Extract migration data
      const content = want.parameters?.editorContent || '';  
      const scrollPos = want.parameters?.scrollPos || 0;  
// 2. Restore interface status
      this.setText(content);  
      this.setScroll(scrollPos);  
    }  
  }  
}  
```  

### 3. Single instance adaptation: onNewWant()
```typescript  
onNewWant(want: Want, launchParam: any) {  
  if (launchParam.launchReason === 'CONTINUATION') {  
// Process the connection request of the started application
    const data = want.parameters?.migrationData;  
    this.updateData(data);  
  }  
}  
```  


## 3. Continuing process optimization strategy
### 1. Data transmission optimization
- **Incremental Synchronization**: Only transfer of change data to reduce traffic consumption
```typescript  
// Calculate data differences
const diff = calculateDiff(oldData, newData);  
wantParam['deltaData'] = diff;  
```  
- **Compression transmission**: Real-time compression of large file data
```typescript  
const compressed = await zipFile(bigData);  
wantParam['compressedData'] = compressed;  
```  

### 2. Exception handling matrix
| Exception Type | Solution |
|----------------|-----------------------------------|  
| Network interruption | Local cache data, automatically transfer after network recovery |
| Device offline | Prompt the user's device status and provide a list of alternative devices |
| Data format incompatible | Version negotiation mechanism, automatic conversion of data format |


## 4. Practical cases: Document editing continuation
### 1. Source (mobile phone) save logic
```typescript  
onContinue(wantParam) {  
// Save document content, cursor position, format settings
  wantParam['docContent'] = this.editor.getContent();  
  wantParam['cursorPos'] = this.editor.getCursor();  
  wantParam['formatStyle'] = this.editor.getStyle();  
}  
```  

### 2. Target side (tablet) recovery logic
```typescript  
onCreate(want) {  
  if (isContinuation(want)) {  
    const content = want.parameters.docContent;  
    const pos = want.parameters.cursorPos;  
// Restore document content and cursor position
this tablet editor.setContent(content);
this tablet editor.setCursor(pos);
  }  
}  
```  


## Summary: Three Principles of Continuing Development
1. **Data minimization**: Only transfer necessary states to avoid redundant data
2. **Compatibility priority**: Version negotiation mechanism ensures seamless connection between new and old devices
3. **Fault-tolerant design**: A guarantee solution for abnormal scenarios such as network fluctuations and equipment offline
