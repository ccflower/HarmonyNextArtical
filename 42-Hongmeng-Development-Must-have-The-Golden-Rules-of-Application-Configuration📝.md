
# Hongmeng Development Must-have: The "Golden Rules" of Application Configuration📝

hello!I am Xiao L, the female programmer who "plays with configuration files" in Hongmeng development~ Do you know?An application is like a performance, the package name is "the only ticket number", the icon is "poster", and the permission is "admission permit".Today, let’s talk about the core elements of Hongmeng application configuration, and see how to make the application "compliant and eye-catching" through `config.json` and `module.json5`~


## 1. Package name (bundleName): the application's "unique ID card" 🆔
### (I) Naming Rules
1. **Reverse Domain Name Principle**:
Format: `com.[Company/Organization Name].[Application Name]`
✅ Correct examples: `com.harmonyos.clock`, `io.github.littleL.calendar`
❌ Error example: `com.example.MyApp` (including capital letters), `app.clock` (without prefix)

2. **Character Limit**:
- Only lowercase letters, numbers, and dot numbers are allowed
- Length ≤255 characters, cannot start/end with dot

### (II) Multiple Module scenes
**Scenario**: The main Module and HAP Module coexist
```json
// Main Module (entry)
{
  "app": {
    "bundleName": "com.example.main"
  }
}

// Function Module (feature)
{
  "app": {
"bundleName": "com.example.feature.share" // It is recommended to include the main package name prefix
  }
}
```


## 2. Icons and tags: "Visual Business Card" for the application🎨
### (I) Icon configuration
1. **Format and Size**:
- Main icon: PNG format, providing multiple sizes such as `48px`, `72px`, `96px`, `144px`, `192px`, etc.
- Adapt to different devices:
- Mobile phone/tablet: Use the adaptive icon under the `media_element` directory
- Smart wearable: Provide circular icons (such as `1.0x`, `2.0x` ratio)

2. **Configuration Example**:
```json
{
  "app": {
    "icon": {
"src": "$media:icon", // Main icon path
      "adaptiveIcon": {
"foreground": "$media:icon_foreground", // Foreground map
"background": "$color:icon_bg" // Background color
      }
    }
  }
}
```

### (II) Tag configuration
1. **Multi-language support**:
- Main tag: `"label": "$string:app_name"`
- Strings are defined in `resources/base/strings/strings.json`:
     ```json
     {
"app_name": "My app",
       "app_name_en": "My App"
     }
     ```  
- The system automatically switches according to the device language

2. **Scenario-based tag**:
   ```json
   {
     "abilities": [
       {
         "name": ".MainAbility",
         "label": {
"phone": "$string:app_name", // Mobile phone tag
"tablet": "$string:app_name_tablet" // Tablet end tag
         }
       }
     ]
   }
   ```


## 3. Version statement: Application "Growth Log" 📅
### (I) Double version number mechanism
| Fields | Functions | Examples |
|------------|-------------------------------|---------------|
| `name` | Version number displayed to the user | `1.0.0` |
| `code` | Build version number (for program judgment) | `100` |

### (II) Upgrade strategy
1. **Mandatory Upgrade**:
Compare user `code` with the latest version on the server. If it is lower than, it will prompt an upgrade
   ```typescript
   if (currentVersionCode < latestVersionCode) {
     showUpdateDialog();
   }
   ```

2. **Grayscale Release**:
Control the grayscale range through `code` (such as `code % 100 < 20` means 20% of users are visible)


## 4. Device type configuration: Application "stage access list" 🎭
### (I) Supported device types
```json
{
  "deviceType": [
"phone", // Mobile phone
"tablet", // Tablet
"wearable", // Smart wear
"tv", // Smart screen
"car", // Car machine
"smartVision" // Smart Vision Device
  ]
}
```

### (II) Differentiated configuration
**Scenario**: Only display a certain component on mobile phones and tablets
```typescript
@Entry
@Component
struct MainPage {
  build() {
    if (DeviceType.isPhone() || DeviceType.isTablet()) {
Button('Mobile-exclusive feature');
    }
  }
}
```


## 5. Permission configuration: Application's "Resource Access License"🔑
### (I) Permission classification
| Type | Sample permissions | Application method |
|--------------|-------------------------------|------------------------|
| **System Permission** | `ohos.permission.CAMERA` | Declaration in `config.json` |
| **Sensitive Permission** | `ohos.permission.READ_CONTACTS`| Requires dynamic authorization for users |
| **Custom Permission** | `com.example.permission.MY_PERMISSION` | Custom declaration + verification |

### (II) Configuration example
1. **System permission application**:
```json
{
  "module": {
    "reqPermissions": [
      {
        "name": "ohos.permission.READ_USER_STORAGE",
"reason": "$string:permission_read_storage_desc", // Reason for permission application
        "usedScene": {
"ability": [".MainAbility"], // Ability involved
"when": "inUse" // Apply for use
        }
      }
    ]
  }
}
```

2. **Dynamic permission request**:
```typescript
import { Permissions } from '@ohos.security.permission';

// Apply for camera permissions
Permissions.requestPermissionsFromUser([Permissions.CAMERA])
  .then((data) => {
    if (data[0].code === Permissions.Status.GRANTED) {
      startCamera();
    }
  });
```


## 6. Complete configuration file example: `module.json5` parsing 📄
```json5
{
"srcEntry": "ets/abilityStage/AppStage", // Stage model entry
  "module": {
"package": "com.example.myapp.entry", // Module package name (recommended to be the same as the main package name)
"name": ".MyApplication", // Application class name
"deviceType": ["phone", "tablet"], // Support devices
    "abilities": [
      {
"name": ".MainAbility", // Ability name
"srcPath": "src/ets/abilities/MainAbility", // Code path
"icon": "$media:icon_ability", // Ability icon
"label": "$string:main_ability_label", // Tag
"launchType": "standard" // Start mode
      }
    ],
    "reqPermissions": [
      {
"name": "ohos.permission.LOCATION", // Locate permissions
        "usedScene": {
          "ability": [".MapAbility"],
"when": "always" // Always need
        }
      }
    ]
  },
  "app": {
"bundleName": "com.example.myapp", // The application's unique package name
    "version": {
      "name": "1.1.5",
      "code": 115
    },
    "label": "$string:app_name",
    "icon": "$media:app_icon"
  }
}
```


## 7. Pit avoidance guide: "Traps list" in configuration ⚠️
### (I) Package name conflict
**Consequence**: Causes the app to be unable to install or overwrite old versions
**Solution**:
- Use the `bundleName` generation tool before publishing (such as reverse domain name generator)
- Make sure that the sub-Module package name contains the main package name prefix in multiple module scenarios

### (II) Icon stretching and blur
**Reason**: Multi-size icons are not provided, the system automatically zooms
**Solution**:
- Use `Adaptive Icon` to adapt to pixel density of different devices
- Press `en-US/zh-CN` and `device-type` to store the icons in the `resources` directory

### (III) Excessive application
**Risk**: Rejected from the application market or complained about user privacy
**Best Practice**:
- Apply only for necessary permissions and follow the principle of "minimum enough"
- Sensitive permissions adopt the "dynamic application + scenario description" mode


## 8. Future trend: "Intelligent evolution" of configuration🤖
### (I) Automatic permission adaptation
In the future, it may use AI to analyze code logic, automatically generate the required permission list to reduce manual configuration errors

### (II) Dynamic icon switching
Supports automatic replacement of application icons based on time, place, and user habits, such as:
```json
{
  "icon": {
    "day": "$media:icon_day",
    "night": "$media:icon_night"
  }
}
```

### (III) Cross-device configuration synchronization
In distributed scenarios, the configuration parameters of multiple devices are automatically synchronized, such as:
- Configure the font size on the mobile phone, and automatically apply the same settings on the tablet/smart screen


## Summary: The "Golden Rule" of Configuration📌
**Application compliance = (package name uniqueness × permission rationality) ÷ Configuration redundancy**
- Package name: reverse domain name + all lowercase, eliminate conflicts
- Icon: Multi-size + adaptive, visual uniformity
- Permissions: Minimum necessary + dynamic application, protecting privacy
- Version: Semantic naming + double version number, clear iteration
