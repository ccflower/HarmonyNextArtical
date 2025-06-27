# 🔬 HarmonyOS precision control application: three major jump methods to combat

## 1. Three accurate jump solutions

### 1. openLink: URL reaches target directly
- **Core role**: Open the target application specified page directly through URL
- **Standard points**:
- `link`: Target URL (such as map coordinates `geo:39.9, 116.4`)
- `options.appLinkingOnly`: Whether to jump with App Linking only

```typescript
// Open the map to view coordinates
context.openLink('geo:39.9042,116.4074', { appLinkingOnly: false });
```

### 2. startAbility: precise control of Want object
- **Core Advantages**: Support more parameter configurations, higher flexibility
- **Key parameters**:
- `action`: Target action (such as `ohos.want.action.viewData`)
- `uri`: Target URI (such as `mailto:user@example.com`)

```typescript
// Reply email application to send feedback
const want = {
  action: 'ohos.want.action.sendToData',
uri: 'mailto:support@harmony.com?subject=feedback',
  entities: ['entity.system.browsable']
};
context.startAbility(want);
```

### 3. Web component interception and jump
- **Applicable scenarios**: Intercept specific protocol jumps in WebView native applications
- **Core Logic**: Intercept URLs and process them through `onLoadIntercept`

```typescript
// Intercept weather protocol jumps to weather application
controller.onLoadIntercept((event) => {
  const url = event.data.getRequestUrl();
  if (url.startsWith('weather:')) {
context.openLink(url); // Jump weather application
return true; // Prevent web loading
  }
});
```  


## 2. Version compatibility instructions

| Features | API 12+ Support | API 11 and before |
|--------------|--------------|----------------|  
| Explicit Ability jump | ❌ (Abandoned) | ✅ (BundleName required) |
| App Linking    | ✅           | ❌             |  
| Deep Linking   | ✅           | ✅             |  


## 3. Practical best practices

1. **Not installed processing**
- Check whether the application exists before jumping:
     `common.getAbilityInfo({ bundleName: 'com.target.app' })`  

2. **Security of Parameters**
- Sensitive parameters are passed through `Want.parameters` instead of URI
- App Linking preferentially uses HTTPS protocol

3. **User Experience Optimization**
- Provide application selection instructions when type jump
- A clear error message is given when the jump fails


## Summarize
HarmonyOS provides three precise jump methods: openLink/startAbility/Web interception. Developers can choose according to the scene: directly connect to the URL, startAbility for complex parameter control, and interception for web scenarios.Pay attention to the differences in API versions, do uninstalled processing and parameter security control to achieve a smooth cross-application collaboration experience.
