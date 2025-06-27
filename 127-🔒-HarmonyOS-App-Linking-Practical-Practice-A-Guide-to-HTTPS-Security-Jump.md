# 🔒 HarmonyOS App Linking Practical Practice: A Guide to HTTPS Security Jump

## 1. Core advantages of App Linking

### 1. Triple safety guarantee
- **Domain name verification**: Verify domain name ownership through the server `applinking.json` file
- **HTTPS Encryption**: All links are transmitted using the HTTPS protocol
- **Anti-forgery mechanism**: Automatically direct browser for illegal domain name jump

### 2. Cross-scene capability
| Scenarios | Application Examples | Advantages |
|--------------|---------------------------|-----------------------|  
| Scan the QR code to go directly | Scan the QR code to open product details | Anti-malice links |
| Social Sharing | Share in-app page to social platform | Open directly after installing, jump to download page without installing |
| Advertising traffic | Click on ads to go directly to in-app activities | Accurate tracking of sources |


## 2. Four-step process for accessing

### 1. AGC console activation service
1. Log in to AGC and enter "Growth > App Linking" to open the service
2. Record the app ID of the app (in "Project Settings > General")

### 2. Server configuration verification file
- Create `.well-known/applinking.json` on the domain name server`
- File content example:
  ```json
  {
    "app_id": "your_app_id_here"
  }
  ```

### 3. AGC associated domain name
1. Fill in the application's URL domain name in AGC (such as `harmony.example.com`)
2. Turn on domain name verification and publish, waiting for system verification

### 4. Process links in-app
```typescript
import { UIAbility, Want } from '@ohos.app.ability.common';
import { url } from '@ohos.arkts';

export default class EntryAbility extends UIAbility {
  onCreate(want: Want) {
    const uri = want.uri;
    if (uri) {
      const urlObj = url.URL.parseURL(uri);
      const action = urlObj.params.get('action');
      switch(action) {
        case 'product': this.openProductDetail(urlObj.params.get('id'));
        case 'event': this.openEventPage(urlObj.params.get('eventId'));
      }
    }
  }
}
```  


## 3. Safe jump implementation

### 1. Initiate App Linking jump
```typescript
import { common } from '@ohos.app.ability.common';

const context = this.getContext(this) as common.UIAbilityContext;
const link = 'https://harmony.example.com/shop/product?id=123';
const options = {
appLinkingOnly: true, // Force use of App Linking
openInBrowser: true // Redirect the browser when the domain name verification fails
};
context.openLink(link, options);
```  

### 2. No processing logic installed
The system will automatically handle uninstalled situations:
1. The domain name verification has been passed but the application is not installed → Jump to the application market download page
2. Domain name verification failed → Open the browser to access the link directly


## 4. Practical scenario examples

### 1. Scan the QR code to go to the product page
```typescript
// Generate App Linking with Parameters
const generateProductLink = (productId: string) => {
  return `https://harmony.example.com/product?id=${productId}&action=view`;
};

// Scan the code and jump
context.openLink(generateProductLink('P001'), { appLinkingOnly: true });
```  

### 2. Social sharing with reference page
```typescript
// Share link with reference
const shareLink = 'https://harmony.example.com/event?eid=567&from=social';
shareToWeChat(shareLink);

// In-app processing sharing link
onCreate(want) {
  const eid = url.URL.parseURL(want.uri).params.get('eid');
  this.loadEventDetail(eid);
}
```  


## 5. Development precautions

1. **Domain name configuration**
- HTTPS protocol must be used
- Verification files must be placed under `.well-known` in the root directory of the server

2. **Security of Parameters**
- Sensitive parameters are recommended to be encrypted and placed in the URL
- Preferred to pass parameters using URL Query instead of Path

3. **Compatibility**
- API 12 and above support
- Older versions of devices are automatically downgraded to normal Deep Linking


## Summarize
App Linking achieves secure and reliable inter-application jump through domain name checksum HTTPS encryption.Developers only need four steps to configure (AGC activation → server verification → domain name association → in-app processing) to provide a safe one-click direct experience in scenarios such as scanning codes and sharing, and automatically handle uninstalled scenarios to improve user conversion rate and security.
