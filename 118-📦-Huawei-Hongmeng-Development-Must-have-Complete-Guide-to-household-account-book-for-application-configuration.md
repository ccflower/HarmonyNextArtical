# 📦 Huawei Hongmeng Development Must-have: Complete Guide to "household account book" for application configuration

> As a developer who failed to release the application due to configuration errors, today I want to share the "Pipe Avoidance Guide" for Hongmeng application configuration!The first time I submitted the application market was rejected because the package name was written incorrectly. Now I summarized these bloody and tears experiences into strategies~


## 1. Application package name: the application's "ID number"

### 1. Naming rules (as unique as ID number)

- **Reverse domain name method**: You must use `com.example.application name`, such as `com.harmony.demo`
- **Character limit**: Only lowercase letters, numbers, and dots can not start/end with dots
- **Length Limit**: No more than 255 characters, too long to remember

### 2. Practical configuration (config.json)

```json
{
  "app": {
"bundleName": "com.example.harmonyApp" // Package name configuration
  }
}
```  

### 3. Common Errors

- Written in capital: `Com.example.demo` ❌ Must be all lowercase ✅
- Start with a dot: `.example.demo` ❌ Correct `com.example.demo` ✅
- Duplicate package name: Submitting the market will prompt conflicts


## 2. Icons and tags: "Pretty and Name" of the application

### 1. Three elements of icon configuration

| Type | Size | Placement |
|--------------|-----------------------|-------------------------|  
| Application icon | 108px×108px | resources/base/media/ |
| Notification icon | 24px×24px | resources/base/media/ |
| Startup icon | 144px×144px | resources/base/media/ |

### 2. Tag configuration (multi-language adaptation)

```json
// Reference string resources in config.json
{
  "app": {
"label": "$string:app_name" // Quote string resources
  }
}

// Chinese tag (resources/base/strings/strings.json)
{
"app_name": "Hongmeng Application"
}

// English tag (resources/en/strings/strings.json)
{
  "app_name": "Harmony App"
}
```  

### 3. Icon FAQ

- Icon blur: must be used to convert vector images to png to avoid stretching
- The size is wrong: use 24px for notification icons, do not use large icons to support small positions


## 3. Version statement: "Age proof" of application

### 1. Version number two parts

| Name | Function | Example |
|--------------|-----------------------|-----------------------|  
| Show version number | Version seen by the user | "1.0.0" |
| Build version code | System identification version | 100 (corresponding to 1.0.0) |

### 2. Configuration example (config.json)

```json
{
  "app": {
    "version": {
"name": "1.0.0", // The version displayed to the user
"code": 100 // The version code used by the system
    }
  }
}
```  

### 3. Version update strategy

- Iterative update: subversion number +1, such as 1.0.0→1.1.0
- Major update: Major version number +1, such as 1.0.0→2.0.0
- Revision update: Revision number +1, such as 1.0.0→1.0.1


## 4. Device type: "Activity range" of the application

### 1. Supported device types

```json
{
  "deviceType": [
"phone", // Mobile phone
"tablet", // Tablet
"watch", // watch
"car", // Car
"tv" // TV
  ]
}
```  

### 2. Equipment adaptation skills

- Mobile phone + tablet: adapt to different screens with responsive layout
- Watch: simplify the interface and highlight the core functions


## 5. Permission configuration: "Pass" for the application

### 1. Common permission configuration

```json
{
  "module": {
    "reqPermissions": [
      {
"name": "ohos.permission.READ_USER_STORAGE", // Read storage permissions
        "usedScene": {
          "when": "inUse",
"description": "Read photo album pictures"
        }
      },
      {
"name": "ohos.permission.ACCESS_FINE_LOCATION", // Locate permissions
        "usedScene": {
          "when": "inUse",
"description": "Required to obtain location information"
        }
      }
    ]
  }
}
```  

### 2. Time to apply for permissions

| Permission Type | Application Time | Sample Scenario |
|----------------|-------------------------|-----------------------|  
| Normal permissions | Application at the start of the application | Network access |
| Dangerous permissions | Pop-up application during use | Positioning, storage |


## 6. Complete configuration example: "household account book" for the application

```json
{
  "app": {
"bundleName": "com.example.harmonyDemo", // Package name
"vendor": "HarmonyDeveloper", // Manufacturer name
    "version": {
"name": "1.0.0", // Show version
"code": 100 // Build version code
    },
"label": "$string:app_name", // Application tag
"icon": "$media:app_icon" // Application icon
  },
  "deviceType": [
"phone", "tablet" // Support devices
  ],
  "module": {
    "package": "com.example.harmonyDemo.entry",
    "name": ".MyApplication",
    "reqPermissions": [
      {
        "name": "ohos.permission.READ_USER_STORAGE",
        "usedScene": {
          "when": "inUse",
"description": "Read photo album pictures"
        }
      },
      {
        "name": "ohos.permission.MICROPHONE",
        "usedScene": {
          "when": "inUse",
"description": "Microphone required"
        }
      }
    ]
  }
}
```  


## 7. Guide to avoid pits on the shelves: Configuration errors and inventory points

1. **Packet name error**:
- Written as `com.Example.demo` (included capital) → The market refuses to be listed
- Correct: `com.example.demo`

2. **Icon Question**:
- The icon size is incorrect (such as 108px notification icon) → display blur
- Correct: Place the corresponding size icon according to the specifications

3. **Permission missing**:
- Read storage does not have permissions → crashes during runtime
- Correct: Configure `ohos.permission.READ_USER_STORAGE`


## The last thought

When I submitted the application market for the first time, I was rejected because the package name was written as `com.example.HarmonyApp` (including capitalization), and I changed it 3 times before I passed~ Now I have developed the habit of checking the specifications after configuration~
