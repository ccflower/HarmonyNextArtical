#Hongmeng Next Breakpoint Adaptation: The Golden Rules for Cross-device Layout

In Hongmeng development, breakpoint adaptation is the key to achieving "one set of code runs multiple ends".This article analyzes the core concepts and practical skills of breakpoints to help you create an elastic layout that is suitable for mobile phones/tablets/cars~


## 1. Breakpoint: "tick mark" of screen size
### 1. Standard breakpoint interval
| Breakpoint name | Width range (vp) | Typical devices |
|----------|----------------|------------------|  
| XS | [0, 320) | Folding screen (folded state) |
| sm | [320, 600) | Mobile |
| md | [600, 840) | Tablet |
| lg | [840, +∞) | Large screen/car machine |

### 2. Dynamic adaptation logic
```mermaid  
graph TD  
A[Window size change] --> B[Trigger breakpoint switching]
B --> C [Update UI layout]
C --> D[Component Rerender]
```  


## 2. Grid component: "Swiss Army Knife" that is adapted to breakpoints
### Core attribute analysis
```arkts  
GridRow({  
  breakpoints: {  
value: ['600vp', '840vp'], // Define breakpoint threshold
    reference: BreakpointsReference.WindowSize  
  }  
}) {  
  GridCol({  
    span: {  
xs: 12, // Small screen full
sm: 6, // 1/2 in the middle screen
md: 4, // Big screen 1/3
lg: 3 // Super large screen and body 1/4
    }  
  }) {  
// Component content
  }  
}  
```  

### Practical case: picture wall layout
```arkts  
@Entry  
@Component  
struct ImageWall {  
  build() {  
    GridRow() {  
// Single column for small screens, multiple columns for large screens
      GridCol({ span: { sm: 12, md: 6, lg: 4 } }) {  
        Image('').width('100%').aspectRatio(1)  
Text('Image Description').center()
      }  
// Repeat components to achieve grid effect
    }  
.columnsTemplate('1fr 1fr') // Two columns with large screen
    .rowsTemplate('1fr 1fr')  
    .gap(10)  
  }  
}  
```  


## 3. Navigation component: breakpoint-driven navigation transformation
### Responsive Navigation Case
```arkts  
@Entry  
@Component  
struct AdaptiveNav {  
  @State isLargeScreen: boolean = false  
  build() {  
    Navigation() {  
      if (isLargeScreen) {  
// Large screen segmented navigation
        Row() {  
Column() { /* Sidebar */ }
Column() { /* Content area */ }
        }  
      } else {  
// Navigation at the bottom of the small screen
BottomNavigation() { /* Bottom tag */ }
      }  
    }  
    .onBreakpointChange((bp) => {  
      isLargeScreen = bp >= '600vp'  
    })  
  }  
}  
```  


## 4. Dynamic adaptation: Real-time response to window changes
### 1. Three steps for breakpoint monitoring
1. Declare status reception breakpoint changes
2. Update status in `onBreakpointChange`
3. Render different layouts according to state conditions

### 2. Practical code: dynamic display of sidebar
```arkts  
@Entry  
@Component  
struct DynamicLayout {  
  @State showSideBar: boolean = false  
  build() {  
    Row() {  
      if (showSideBar) {  
Column() { /* Sidebar Content */ }
          .width('25%')  
      }  
Column() { /* Main content area */ }
        .flexGrow(1)  
    }  
    .onBreakpointChange((bp) => {  
showSideBar = bp >= '600vp' // The sidebar is always displayed on the large screen
    })  
  }  
}  
```  


## 5. Optimization skills: Make adaptation more efficient
1. **Breakpoint grouping**: Merge similar breakpoints (such as `md` and `lg` use the same layout)
2. **Component multiplexing**: Encapsulate `@Builder` components to adapt to different breakpoints
3. **Performance Optimization**: Avoid re-rendering when switching breakpoints frequently (using `LazyForEach`)


## Summary: The "Three Principles" of Breakpoint Adaptation
1. **Interval priority**: First define the core breakpoint interval, then fill in details
2. **Component linkage**: Grid/Navigation and other components collaborate to realize layout transformation
3. **Planetary optimization**: Start with basic adaptation and gradually improve edge scenarios
