# 🔗 HarmonyOS Deep Linking Practical Practical Practice: "Digital Key" that flexibly defines link rules

> As a developer who was once trapped by rules in application jumps, today I want to reveal the flexible gameplay of Deep Linking!When I first used a custom scheme to achieve cross-application jump, my colleague thought I had changed the underlying layer of the system, but in fact I just mastered the skills of using this "master key" ~


## 1. Deep Linking: "Arbitrary Door Key" between the application

### 1. What is Deep Linking?

Imagine you have a string of master keys:
- Traditional jumps are like fixed keys, and can only open specific door locks (fixed application)
- Deep Linking is like a master key, customize the key shape (scheme) which door you want to open which door you want

HarmonyOS's Deep Linking core capabilities:
- **Custom protocol**: Define the door opening rules yourself (such as `myapp://`)
- **Parameter transfer**: Open the door with parameters (such as `myapp://product?id=123`)
- **Flexible adaptation**: You can jump to the browser when there is no application installed (alternative door opening solution)


### 2. Three core features

| Features | Traditional Jump | Deep Linking |
|--------------|---------------------|---------------------|  
| Protocol Definition | System Fixation | Customization (such as `shop://`) |
| Parameter support | Simple pass | Complex parameter structure |
| Not installed processing | Cannot jump | Browser or download page can be jumped |


## 2. Implementation principle: "making process" of master key

### 1. Target application key module (register URL skill)

```json
// key module in module.json5
{
  "module": {
    "abilities": [
      {
        "skills": [
          {
            "uris": [
              {
"scheme": "food", // Custom protocol name (key shape)
"host": "map", // Domain name (lock type)
"path": "/location" // Path (keyhole location)
              }
            ]
          }
        ]
      }
    ]
  }
}
```  

### 2. The initiator sets up the key (constructs the link)

```typescript
// Construct the door opening key (Deep Linking link)
const link = "food://map/location?lat=39.9&lng=116.4&address=Wangfujing";
```  

### 3. System unlock (match jump)

1. The system presses "key shape" (scheme) to find matching applications
2. After finding it, press "Keyhole Position" (path) to open the corresponding page
3. Jump to the browser when no application is found (alternative solution)


## 3. Practical configuration: "Customization Guide" for key molds

### 1. Detailed explanation of URL skill parameters

| Parameters | Functions | Examples |
|--------------|-----------------------|-----------------------|  
| scheme | protocol name (key shape) | "food" |
| host | Domain name (lock type) | "map" |
| path | path (keyhole location) | "/location" |
| pathStartWith | Path prefix matching | "/api/" |
| pathRegex | path regular matching | "/product/\\d+" |


### 2. Complex matching example (supports regularity)

```json
{
  "uris": [
    {
      "scheme": "shop",
      "host": "product",
"pathRegex": "/detail/\\d+" // Match /product/detail/123 and other paths
    }
  ]
}
```  

**Match link example**:
`shop://product/detail/123` → Match successfully
`shop://product/detail/abc` → Match failed


## Four, three ways to unlock the lock: "Using skills" of the key

### 1. openLink interface: directly insert the key and open the door

```typescript
import { common } from '@ohos.app.ability.common';

// Open the map application with the key
const context = this.getContext(this) as common.UIAbilityContext;
const link = "geo://map/location?lat=37.77&lng=122.42";
context.openLink(link, { appLinkingOnly: false }); // Allow Deep Linking
```  

### 2. startAbility interface: open the door with parameters

```typescript
import { common } from '@ohos.app.ability.common';

// Open the product details page with parameters
const want = {
  action: 'ohos.want.action.viewData',
  uri: "shop://product/detail/123",
parameters: { from: 'cart' } // Extra parameters
};
context.startAbility(want);
```  

### 3. Web component interception: insert keys in web pages

```typescript
import { webview } from '@ohos.arkweb';

// Identify Deep Linking on the web page and jump
this.controller.onLoadIntercept((event) => {
  const url = event.data.getRequestUrl();
  if (url.startsWith("shop://")) {
context.openLink(url); // Jump to the corresponding application
return true; // Prevent web page from loading
  }
});
```  


## 5. Practical scenario: "Application Cases" of Keys

### 1. Takeaway app jump to map (navigate to store)

```typescript
// Construct Deep Linking with coordinates
const shopLink = "geo://map/location?lat=39.9&lng=116.4&shopName=Haidilao";

// Jump the map application
context.openLink(shopLink, { appLinkingOnly: false });
```  

### 2. Social sharing with reference page (product sharing)

```typescript
// Generate sharing link with product ID
const shareLink = `shop://product/detail/123?from=social&userId=567`;

// Share to WeChat
shareToWeChat(shareLink);

// Processing by the receiver
onCreate(want) {
  const productId = url.URL.parseURL(want.uri).params.get('id');
  loadProductDetail(productId);
}
```  


## 6. Safety precautions: "Anti-theft guide" for keys

### 1. Three measures to prevent malicious counterfeiting

1. **scheme naming anti-conflict**:
Written in reverse with the company domain name (such as `com.example.shop`)
❌ Danger naming: `map` ✅ Safe naming: `com.example.map`

2. **Parameter encryption transmission**:
Sensitive parameter encryption (such as `token=encrypted string`)

3. **Backend verification source**:
In-app verification link source domain name


### 2. Not installed processing best practices

```typescript
context.openLink(link, {
  appLinkingOnly: false,
openInBrowser: true, // Open the browser when it is not installed
browserUrl: "https://shop.com/product/123" // Custom browser opening address
});
```  


## 7. The difference from App Linking: "Level Difference" of Keys

| Comparison | Deep Linking | App Linking |
|--------------|------------------------|------------------------|  
| Protocol Type | Custom Scheme | HTTPS Standard Protocol |
| Security | Easily fake | Domain name verification + HTTPS encryption |
| Applicable scenarios | Internal jump of enterprises | External sharing and advertising traffic |
| Not installed processing | Need to custom processing | Automatic jump to the application market |


## The last thought

I remember when I used Deep Linking for social sharing for the first time, my custom `share://` protocol was criticized by my colleagues for being like "black magic". Now I use this solution to achieve seamless jump between various applications. The user said "It's as convenient as magic."
