
# Hongmeng UTD Detailed Explanation: Cross-end Collaboration Key for Standardized Data Types🔑

Hongmeng Next's UTD (Uniform Type Descriptor) solves the problem of cross-application and cross-device data identification by standardizing data types.This article uses streamlined language to analyze the core concepts and practical points~


## 1. UTD core: "Global unique ID card" of data type🆔
### Definition and Function
- **Unique ID**: Each data type has a unique ID (such as `general.image`) to avoid type ambiguity
- **Hydraft Structure**: Classification system similar to file directories (`media > image > photo`)
- **Cross-end compatibility**: Unify application/device's awareness of data types to improve interaction efficiency

### Core attribute table
| Properties | Description | Example |
|---------------|-------------------------------|-------------------------------|  
| typeId | Unique Identifier | `com.example.custom.pdf` |
| belongingTo | Parent type (multi-level) | `general.document` |
| description | Type Description | "Custom PDF Document" |
| fileExtensions| Associated file suffix | [".pdf", ".pdfx"] |
| mimeType | MIME type | "application/pdf" |


## 2. Preset type: System-level data classification "infrastructure" 🚧
### Common hierarchy examples
```  
media  
├─ image          (general.image)  
│  ├─ photo       (image.photo)  
│  └─ graphic     (image.graphic)  
└─ video          (general.video)  
    └─ movie      (video.movie)  
```  

### Typical application scenarios
- **File Sharing**: The system automatically matches applications that support `general.image` based on UTD (such as albums, editors)
- **Data synchronization**: When transmitting across devices, UTD ensures that the receiver correctly parses the `video.movie` type videos


## 3. Custom type: "movable type module" that extends system capabilities🔧
### Create steps
1. **Define Type**
   ```typescript  
   import { TypeDescriptor } from '@ohos.uniformTypeDescriptor';  
   const customType = new TypeDescriptor({  
     typeId: 'com.example.app.custom_audio',  
belongingTo: ['general.audio'], // Inherited from audio type
description: 'Custom lossless audio',
     fileExtensions: ['.lossless'],  
     mimeType: 'audio/x-lossless'  
   });  
   ```  

2. **Register to the system**
   ```typescript  
   TypeDescriptor.register(customType);  
   ```  

3. **Cross-app use**
   ```typescript  
// Sender
   const data = new File('music.lossless', customType.typeId);  
   featureAbility.startAbility({  
     action: 'android.intent.action.SEND',  
     type: customType.mimeType,  
     extra: { 'android.intent.extra.STREAM': data  
   });  

// Recipient
   const receivedType = Ability.getWant().type;  
   if (TypeDescriptor.isCompatible(receivedType, 'com.example.app.custom_audio')) {  
     processLosslessAudio();  
   }  
   ```  


## 4. Compatibility design: Let custom types "integrate into the system ecosystem"🌐
### 1. Parent-child type compatibility
```typescript  
// Check whether it belongs to a certain parent type
TypeDescriptor.isAncestor('general.image', 'image.photo'); // Return true
```  

### 2. Cross-device transmission adaptation
```typescript  
// Automatically convert to common type during transmission
if (device.type === 'wearable') {  
  const fallbackType = TypeDescriptor.getFallbackType('com.example.custom_audio');  
sendDataWithType(fallbackType); // Automatic downgrading to general.audio
}  
```  

### 3. Conflict resolution strategy
| Scenarios | Solutions |
|---------------------|-----------------------------------|  
| Third-party applications define the type of the same name | Priority is given priority to using the system preset type, and custom types must be declared `priority` |
| Type attribute changes | Release a new version through `TypeDescriptor.update()` |


## 5. Practical cases: Customize the "engineering drawing" type📃
### 1. Type definition
```typescript  
const cadType = new TypeDescriptor({  
  typeId: 'com.engineer.app.cad_draw',  
belongingTo: ['general.document', 'engineering'], // Multiple parent types
description: 'CAD engineering drawings',
  fileExtensions: ['.dwg', '.dxf'],  
  mimeType: 'application/cad'  
});  
```  

### 2. Cross-application collaboration
```typescript  
// CAD editor sends drawings
const drawing = new File('project.dwg', cadType.typeId);  
ability.startAbility({  
  action: 'com.engineer.app.ACTION_OPEN_CAD',  
  type: cadType.mimeType,  
  extra: { 'cad_data': drawing }  
});  

// Approve application acceptance and preview
if (TypeDescriptor.isCompatible(want.type, cadType.typeId)) {  
  const renderer = new CadRenderer(want.extra['cad_data']);  
  renderer.preview();  
}  
```  


## Summary: UTD uses the "Three Principles"
1. **Prioritative reuse**: Try to use system preset types (such as `general.image`) to avoid repeated wheel creation
2. **Clear hierarchy**: Custom types need to be clearly attributed (such as `engineering.cad` belongs to `general.document`)
3. **Compatible Design**: Consider cross-default strategy for type across devices and applications
