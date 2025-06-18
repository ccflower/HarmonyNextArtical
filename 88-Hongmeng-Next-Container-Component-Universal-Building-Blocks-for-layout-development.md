#Hongmeng Next Container Component: "Universal Building Blocks" for layout development

Hongmeng Next's container components are like flexible building modules, helping developers build a flexible layout that is adapted to multiple devices.This article analyzes the core capabilities of Row/Column/Flex, with practical skills for adaptive and responsive layout~


## Quick view of 1 and 3 basic containers
### 1. Row: Horizontal arrangement of "storage boxes"
- **Layout Direction**: Spindle horizontal (left → right)
- **Core properties**: `justifyContent` (alignment), `spacing` (spacing)
- **Scene**: Navigation bar, icon group, horizontal menu

### 2. Column: Vertical arrangement of "stack racks"
- **Layout Direction**: The spindle is vertical (upper→down)
- **Core properties**: `alignItems` (cross axis alignment), `flexDirection` (arrange direction)
- **Scenario**: List items, forms, vertical menus

### 3. Flex: Flex layout "Transformers"
- **Layout Direction**: Dynamically switch horizontal/vertical through the `direction` property
- **Core properties**: `flexGrow` (stretch ratio), `flexShrink` (shrink ratio)
- **Scene**: Mixed graphics and text cards, dynamic proportional layout


## 2. Core skills for adaptive layout
### 1. Three magic weapons for space allocation
| Capabilities | Implementation | Code Examples (Key Snippets) |
|------------|-----------------------------------|-----------------------------------|  
| **Elastic stretch** | `flexGrow: 1`Let the component occupy the remaining space | `Blank().flexGrow(1)` |
| **Evenly distributed** | `justifyContent: FlexAlign.SpaceEvenly` | `.justifyContent(FlexAlign.SpaceEvenly)` |
| **Proportional Allocation** | Set width to percentage or `layoutWeight` | `.width('30%')` / `.layoutWeight(2)` |

### 2. Adaptive layout practice
```typescript  
@Entry  
@Component  
struct AdaptiveLayout {  
  build() {  
    Row() {  
Text('Fixed Area').width(100).height(50).bgColor('#FFD700')
Blank().flexGrow(1) // Flexible fill in the remaining space
      Toggle().width(36).height(20)  
    }  
    .width('80%')  
    .height(50)  
    .bgColor('#FFFFFF')  
  }  
}  
```  
**Effect**: The Blank component between Toggle and fixed text will automatically stretch as the width of the parent container changes.


## 3. Responsive layout combination strategy
### 1. Breakpoint adaptation scheme
```typescript  
@Entry  
@Component  
struct ResponsiveDemo {  
  build() {  
    GridRow() {  
// Small screen full, large screen 2/3
      GridCol({ span: { sm: 12, md: 8 } }) {  
        Flex({ direction: FlexDirection.Row }) {  
          Image().width(120).height(80)  
          Column() {  
Text('Top News').fontSize(18)
Text('Introduction...').opacity(0.6)
          }.flexGrow(1)  
        }  
      }  
// 1/3 of the right side of the large screen
      GridCol({ span: { sm: 12, md: 4 } }) {  
Text('Hot Recommended').fontSize(16)
      }  
    }  
.onBreakpointChange(() => { /* Breakpoint switching logic */ })
  }  
}  
```  

### 2. Equipment adaptation skills
- **Screen rotation**: Dynamically adjust `flexDirection` by listening for direction changes through `@ohos.window.info`
- **Multi-terminal adaptation**: Use `if-else` to load different layout components according to device type (mobile phone/tablet)


## 4. Key points of performance optimization
1. **Avoid too deep nesting**: Nesting above three layers will affect rendering performance. `Stack` can be used instead of multi-layer `Column+Row`
2. **Reuse layout components**: Encapsulate common layouts into `@Builder` components to reduce duplicate code
3. **Lazy Loading Policy**: Use `LazyForEach` to delay rendering for non-visible area components


## Summary: The "Three Principles" of Layout Development
1. **Scenario priority**: Select Row horizontally, Column vertically, and Flex for flexible layout
2. **Elastic Design**: Make good use of `flexGrow`/percentage width to deal with screen changes
3. **Component Reuse**: Package reusable layout modules to improve development efficiency
