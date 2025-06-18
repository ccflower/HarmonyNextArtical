
#Hongmeng interface mirror test: Make RTL layout "both and back look good"

hello!I am Xiao L, the female programmer who "plays interface mirroring" in Hongmeng application~ Do you know?When the application enters the Arabic market, the interface needs to be displayed from right to left, and the button position, text direction, and icon arrows must be "mirror flipped"!Today, let’s talk about how to use pseudo-localization test to make the interface “silky with both front and back” in RTL language~


## 1. "Three Questions of Soul" in Mirror Test
### (I) What is RTL layout?
RTL (Right-To-Left) is a layout from right to left, suitable for Arabic, Hebrew and other languages.Core changes:
- **Text Direction**: Read from right to left (such as Arabic `مرحبا` displayed as mirror order)
- **Element arrangement**: Button order is reversed (Confirm → Cancel → Cancel → Confirm)
- **Icon Direction**: Arrow Icon Mirror (→ Change to ←)

### (II) Why do we have to test mirrors?
| "Car overturn scene" of untested mirrors | Real impact |
|-----------------------------|------------------------------|
| The button click area is misaligned | The user clicks "Cancel" but triggers "Confirm" |
| 表格列顺序颠倒              | 数据展示逻辑混乱             |
| Miscellaneous pictures and texts | Pictures are on the left and texts are on the right (violating RTL habits) |
| Animation direction is wrong | Loading animation plays from left to right (reverse visual habits) |

### (III) Mirror test ≠ simple flip!
- **True mirror**: The layout logic is completely reversed (such as FlexLayout spindle direction from row→row-reverse)
- **False flip**: Only visual reversal but the interaction logic has not changed (the click area is still on the left)
**Key**: The consistency of "visual + interaction" must be verified at the same time during testing


## 2. The "four-step" process of mirror testing
### (I) Turn on RTL mode: the code triggers the mirror
```typescript
// Set the system language to Arabic pseudo-localized area
import { AppLocalizer } from '@ohos.app.ability.localization';
AppLocalizer.setLocale('ar-XA'); // XA represents pseudo-test area
AppLocalizer.setLayoutDirection(LayoutDirection.RIGHT_TO_LEFT); // Force RTL layout
```

### (II) Interface "mirror scan" list
#### 1. **Basic Component Test**
| Component Type | Test Points | Expected Effects |
|------------|---------------------------------|------------------------------|
| Text       | 文本方向、标点位置（如句号在左）| 从右向左阅读，标点在文本末尾 |
| Button | Arrange order, click area | Arrange from right to left, click area correctly |
| Image      | 方向性图标（箭头/人物朝向）     | 图标镜像翻转，语义不变       |
| List | List item order, sliding direction | List item is arranged from right to left, sliding smoothly |

#### 2. **复杂布局测试**  
- **Navigation Bar**: Return button on the left (right of the user's viewpoint)
- **Form**: Tags are on the right and input boxes are on the left
- **Dialogue**: Button order (confirm on the left → mirror on the right)

#### 3. **Interactive Logic Test**
- Slide menu: Swipe out from the right edge
- Gesture operation: Swipe right back (corresponding to LTR left back)
- Pop-up layer: pops up from the right side (such as the drawer menu on the right side)

### (III) Tool assistance: Automatically detect mirroring problems
#### 1. Hongmeng Layout Verification Tool
```bash
# Command line checks for RTL layout errors
hdc shell uiautomator dump --rtl > layout_dump.xml
grep "layoutDirection=rtl" layout_dump.xml  # 检查是否应用RTL属性
```

#### 2. Pixel level comparison tool
Use the `PixelPerfect` library to automatically compare pixel differences in LTR/RTL interface:
```javascript
import { PixelPerfect } from '@ohos.testing';

// Compare the benchmark diagram with RTL image diagram
PixelPerfect.compareScreenshots('base_layout.png', 'rtl_layout.png', {
threshold: 0.1, // Allow 10% pixel difference
ignoreRegions: [// Ignore dynamic element regions
    {x: 100, y: 200, width: 200, height: 50}
  ]
});
```

### (IV) Cultural Compliance Inspection
- **Color Taboo**: Avoid using yellow in Arabia (symbolizes death)
- **Icon Meaning**: Disable sensitive elements such as pigs and wine
- **Religious Symbols**: Ensure that no graphics that violate local beliefs are included


## 3. Practical cases: "Metamorphosis" of the social app
### (I) Scenario: Chat interface mirror test
#### 1. **Problem 1: The message bubble direction is incorrect**
- LTR: User message is on the right, and the other party message is on the left
- After mirroring: the user message should be on the left (to the right side of the user's view), but it is still on the right

#### 2. **Solution**
```xml
<DirectionalLayout 
  ohos:orientation="vertical"
ohos:layout_alignment="right" <!-- Right-alignment of user messages -->
ohos:is_rtl_support="true"> <!-- Turn on RTL support -->
  
  <Text 
ohos:text="user message"
    ohos:background_element="$graphic/user_bubble"
    ohos:text_alignment="start" /> <!-- 文本从右开始 -->
</DirectionalLayout>

<DirectionalLayout 
  ohos:orientation="vertical"
ohos:layout_alignment="left" <!-- The other party's message is left aligned -->
  ohos:is_rtl_support="true">
  
  <Text 
ohos:text="Optopian message"
    ohos:background_element="$graphic/other_bubble"
ohos:text_alignment="end" /> <!-- The text ends from left -->
</DirectionalLayout>
```

### (II) Scenario: Product list mirror test
#### 1. **Problem 2: The price tag is inconsistent**
- LTR: The picture is on the left, the price is on the right
- After mirroring: the picture should be on the right, the price should be on the left, but the price should still be on the right

#### 2. **Solution**
```xml
<StackLayout 
  ohos:orientation="horizontal"
  ohos:layout_alignment="center"
ohos:is_rtl_support="true"> <!-- Overall support for RTL -->
  
  <Image 
    ohos:image_src="$media/product_image"
ohos:layout_alignment="right" /> <!-- On the right behind the mirror -->
    
  <Text 
    ohos:text="$string:price"
    ohos:layout_alignment="left"
ohos:text_alignment="end" /> <!-- Price is on the left, text is aligned right -->
</StackLayout>
```


## 4. Pit avoidance guide: "Invalid operation" of mirror test
### (I) "Visual Deception" Trap
**Error practice**: Only test static interface, ignore dynamic content
**Correct posture**:
- Test the layout after dynamic operations such as loading more and refreshing
- 验证列表滑动时元素顺序是否保持RTL  

### (II) Risk of "interaction disconnection"
**Error practice**: The touch event after mirroring is not adjusted
**Correct posture**:
- 使用`getLayoutDirection()`获取当前布局方向  
- Adjust click event coordinate calculation according to direction:
```typescript
let layoutDirection = AppLocalizer.getLayoutDirection();
if (layoutDirection === LayoutDirection.RIGHT_TO_LEFT) {
// Click on the x-axis to reverse
  clickX = screenWidth - clickX;
}
```

### (III) "Third-party components" blind area
**Error practice**: Assume that all components automatically support RTL
**Correct posture**:
- Check the open source library documentation (such as whether to label `supportsRtl=true`)
- 对不支持的组件进行包裹适配：  
```xml
<DirectionalLayout 
  ohos:is_rtl_support="true"
ohos:layoutDirection="rtl"> <!-- Force subcomponent RTL -->
  
  <ThirdPartyChart 
    ohos:layout_width="match_parent"
    ohos:layout_height="300vp" />
</DirectionalLayout>
```


## 5. Future trend: Intelligent mirror layout system
### （一）「自适应镜像」引擎  
AI analyzes interface element semantics and automatically generates RTL layout:
- Button group: Automatically reverse the order and keep the logic (confirm → cancel → cancel → confirm)
- Table: Column order is intelligently adjusted according to field importance

### (II) "Real-time Preview" tool
Added an RTL real-time preview panel to HarmonyOS Studio, which supports one-click switching of layout directions and real-time viewing of mirroring effects.

### (III) "Cultural Perception" Layout
根据地理位置自动适配镜像规则：  
- 中东地区：强制RTL布局  
- Other regions: Default LTR, user can switch manually


## 最后提醒：镜像测试的「黄金法则」
**Mirror Perfection = (Layout Accuracy × Interaction Fluency) ÷ Number of Cultural Conflicts**
- Layout accuracy: All elements are arranged in accordance with RTL specifications
- 交互流畅度：操作延迟＜100ms  
- Number of cultural conflicts: 100% pass rate for sensitive element detection
