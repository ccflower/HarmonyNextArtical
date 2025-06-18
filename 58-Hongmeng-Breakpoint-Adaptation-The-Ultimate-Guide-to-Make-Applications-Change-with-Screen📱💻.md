
#Hongmeng Breakpoint Adaptation: The Ultimate Guide to Make Applications "Change with Screen"📱💻

Hi~ I am Xiao L!In Hongmeng development, adapting to multi-device screen size is like a "puzzle game" - breakpoint adaptation is the most critical puzzle technique.Today, we will take you to unlock the core gameplay of breakpoints, so that the application can be "appeared online" on mobile phones, tablets, and car machines!


## 1. Breakpoint basics: "Scale ruler" of screen size📏
### Why do you need a breakpoint?
- **Fragmentation**: Hongmeng Eco-covered 1-inch watch → 100-inch smart screen
- **Layout flexibility**: Single columns are used for small screens, multiple columns are used for large screens, and split screens are used for cars and computers

### Officially recommended breakpoint division (based on vp units)
| Breakpoint name | Width range (vp) | Typical devices | Layout strategy |
|----------|----------------|------------------------|---------------------------|  
| XS | [0, 320) | Folding screen (expanded state) | Single column layout, button enlargement |
| sm | [320, 600) | Mobile phone (vertical screen) | Dual-column layout, picture column display |
| md | [600, 840) | Tablet (vertical screen)/mobile phone (horizontal screen) | Three column layout, navigation bar permanent |
| lg | [840, +∞) | Tablet (horizontal screen)/smart screen | Four-column layout, supporting suspended windows |

**vp unit description**: Virtual pixel, automatic adaptation of device density (1vp≈1 physical pixel on 160dpi device)


## 2. Grid component: "Swiss Army Knife" with responsive layout 🔧
### Core feature: Allocate column space by breakpoint
```arkts  
@Entry  
@Component  
struct GridResponsive {  
  build() {  
    GridRow({  
      breakpoints: {  
value: ['600vp', '840vp'], // Trigger breakpoint at 600vp and 840vp
reference: BreakpointsReference.WindowWidth // Judging based on window width
      }  
    }) {  
// Picture card component
      GridCol({  
        span: {  
xs: 12, // XS breakpoint: occupies 12 columns (single column)
sm: 6, // sm breakpoint: occupies 6 columns (double columns)
md: 4, // md breakpoint: occupies 4 columns (three columns)
lg: 3 // lg breakpoint: occupies 3 columns (four columns)
        }  
      }) {  
        Card() {  
          Image('https://picsum.photos/300/200')  
            .aspectRatio(3/2)  
Text('Scenery Picture').fontSize(14).margin(8)
        }  
        .margin(8)  
      }  
    }  
.gridTemplateColumns('1fr') // Basic column template, automatically adjusted after breakpoint
  }  
}  
```  

### Advanced skills: hybrid layout + spacing adaptation
```arkts  
GridRow {  
// Column spacing under different breakpoints
  columnGap: {  
    xs: '4vp',  
    sm: '8vp',  
    md: '16vp',  
    lg: '24vp'  
  }  
// In-line elements adaptation
  GridCol {  
Text('Title').fontSize({
      xs: 16,  
      lg: 24  
    })  
  }  
}  
```  


## 3. Navigation component: "Transformers" for cross-device navigation🚀
### Scenario: Navigation difference between mobile phone vs tablets
#### 📱Mobile (SM breakpoint): Stacked navigation
```arkts  
Navigation() {  
// Hamburger menu button
  Column {  
Button('☰ Menu').onClick(() => toggleMenu())
    if (isMenuOpen) {  
      List {  
ListItem { Text('Home') }
ListItem { Text('Setting') }
      }  
    }  
  }  
// Main content area
NavDestination() { /* Content */ }
}  
.mode(NavigationMode.Stack) // Stacking mode
```  

#### 💻 Tablet (lg breakpoint): Column-type navigation
```arkts  
Navigation() {  
  Row {  
// left navigation bar (fixed 20% width)
    Column {  
      List {  
ListItem { Text('Home') }
ListItem { Text('Setting') }
      }  
    }  
    .width('20%')  
// Content area on the right
NavDestination() { /* Content */ }
  }  
.mode(NavigationMode.Split) // Column mode
}  
```  

### Dynamic switch: listen for breakpoint changes
```arkts  
@State currentBreakpoint: string = 'sm'  

Navigation() {  
// Switch navigation mode according to breakpoint
  if (currentBreakpoint === 'sm') {  
// Mobile phone layout
  } else {  
// Tablet layout
  }  
}  
.onBreakpointChange((bp) => {  
  currentBreakpoint = bp  
  if (bp === 'lg') {  
// Large screen scene: Show more navigation items
    addExtraNavItems()  
  }  
})  
```  


## 4. Global adaptation: "linked response" from components to page🌐
### 1. Breakpoint-aware global style
```less  
/* Public style */
@base-font-size: 14vp;  

/* Font sizes under different breakpoints */
@media (max-width: 600vp) {  
  .title { font-size: @base-font-size * 1.2; }  
}  
@media (min-width: 601vp) {  
  .title { font-size: @base-font-size * 1.5; }  
}  
```  

### 2. Picture adaptation strategy
```arkts  
Image('https://example.com/image')  
  .width({  
xs: '100%', // Small screen full of width
lg: '50%' // Large screen displays 50% width
  })  
  .aspectRatio(16/9)  
.objectFit(ImageFit.Contain) // Prevent tensile deformation
```  

### 3. Dynamic adjustment of button size
```arkts  
Button('Buy Now')
  .fontSize({  
    xs: 14,  
    lg: 18  
  })  
  .padding({  
    xs: 8,  
    lg: 16  
  })  
  .margin({  
    xs: 4,  
    lg: 8  
  })  
```  


## 5. Practical cases: News App Cross-device Adaptation 📰
### Scene Description
- **Mobile phone (sm breakpoint)**: Single column layout, pictures occupy the screen width
- **Plate (lg breakpoint)**: Double column layout, news list on the left, details on the right
- **Smart Screen (xl Breakpoint)**: Three-column layout, top navigation + left-side list + right-side details

### Core Code (simplified version)
```arkts  
@Entry  
@Component  
struct NewsApp {  
  @State currentBreakpoint: string = 'sm'  

  build() {  
    GridRow {  
// News list on the left
      GridCol({ span: { sm: 12, lg: 4, xl: 3 } }) {  
List { /* News List */ }
      }  

// News details on the right
      GridCol({ span: { sm: 12, lg: 8, xl: 9 } }) {  
        if (currentBreakpoint === 'sm') {  
// Mobile phone: Details are displayed in full screen
          NewsDetail()  
        } else {  
// Large screen: Details are displayed in columns
Row { /* Mixed picture and text */ }
        }  
      }  
    }  
    .onBreakpointChange((bp) => {  
      currentBreakpoint = bp  
// Preload more pictures in large screen scenes
      if (bp >= 'lg') {  
        preloadHighQualityImages()  
      }  
    })  
  }  
}  
```  


## 6. Guide to avoid pits⚠️
### 1. Avoid excessive breakpoint division
- ❌ Set breakpoints for each device (such as mobile phone A/mobile phone B)
- ✅ Divided by functional area (such as small screen/middle screen/large screen)

### 2. The trap of mixing vp and px
```arkts  
// ❌ Error: px displays inconsistently on devices with different densities
.width('100px')  

// ✅ Correct: Use vp to ensure consistent proportions
.width('100vp')  
```  

### 3. Dynamic layout performance optimization
- Avoid performing complex calculations in `onBreakpointChange`
- LazyLoading of non-home screen content using `LazyForEach`
```arkts  
.onBreakpointChange(async (bp) => {  
// Use setTimeout to avoid blocking the UI
  setTimeout(() => {  
    updateLayout(bp)  
  }, 0)  
})  
```  


## Summary: Breakpoint adaptation to "Four-step process"
1. **Equipment Classification**: Clarify the size range of target equipment (mobile phone/tablet/large screen)
2. **Breakpoint definition**: Dividing vp intervals according to functional requirements (refer to the official standards)
3. **组件适配**：在Grid/Navigation中应用断点配置  
4. **Global linkage**: Achieve consistent experience through styles, pictures, and interactions
