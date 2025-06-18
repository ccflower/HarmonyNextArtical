# application performance optimization: a practical strategy to make your APP "fly"

Hello everyone!I am Xiao L, the female programmer who optimizes performance in the code ~ Recently, I optimized a Hongmeng application that was stuck to the "smart speed" into "silk-slim lightning" - I will tell you all the "Second Tips" and "Tape Avoidance Guide", and by the way, I will tell you a few jokes that make product managers applaud~


## Chapter 1: Four major culprits of performance "hardening"

### 1. Resource management: "chaotic warehouse" in memory
- **Counterexample**: Crazy new objects in a loop are like throwing cardboard boxes in a warehouse. Garbage trucks (GCs) have to be cleaned up frequently, which delays serious matters~
- **Resolution**: Reuse objects, such as creating a "object pool", take them from the pool when used, and put them back without them, and the warehouse is clean in an instant!

### 2. Code quality: "Labyrinth Trap" in the algorithm
- **Counterexample**: Using bubble sorting to process 100,000 pieces of data is like letting a snail pull a truck, so slow that he doubts his life~
- **Resolution**: Change to quick sorting or binary search, and instantly turn from a snail to a cheetah!

### 3. Network request: "traffic jam scene" for data transmission
- **Counterexample**: Each refresh requests 100 data, 90 of which are repeated - equivalent to driving around the third ring to work every day, and the gas money is enough to buy a new car~
- **Resolution**: Add cache!Check whether there is any local area first, and if there is no networking, it can save 80% of traffic and time~

### 4. Equipment compatibility: "The local environment is not suitable for different equipment"
- **Counterexample**: Feed the same "high-definition large-picture package" for the thousand-yuan machine and the flagship machine, and the thousand-yuan machine is "sustained until it crashes"~
- **Resolution**: "Split meals" according to the performance of the equipment, send "compressed miniature pictures" to low-end machines, and put "original painting meal" to high-end machines~


## Chapter 2: Optimization Tips: Let the code "lightly install"

### 1. Memory optimization: "Reduce weight" for the application
1. **Object Reuse Technique**
```typescript
// Counterexample: Create a new object every time you call
function calculates area (radius: number) {
const circle = { radius, area: 0 }; // New object every time
Circle. Area = Math.PI * Radius * Radius;
return circle;
}

// Correct solution: Use global object pool
const Circular object pool = { Radius: 0, Area: 0 };
function calculates area (radius: number) {
Circular object pool. Radius = radius; // Multiplexed objects
Circular object pool. Area = Math.PI * Radius * Radius;
return { ...circular object pool }; // Return copy to avoid external modifications
}
```
*Cold joke*: Reusing objects is like wearing a school uniform. Wearing a set of clothes for three years, saving fabric and troubles~

2. **Twist the garbage in time**
Set unused variables to null in time, such as:
```typescript
let Large picture: ImageBitmap | null = await loadImage('big.jpg');
// After use
Large picture = null; // Tell GC: This garbage can be collected!
```

### 2. Code optimization: "change track" for the algorithm
1. **Use efficient data structures**
- List search uses Map instead of array:
```typescript
// Array search (O(n))
const array = [1, 3, 5, 7];
console.log(array.includes(5)); // traversal search

// Map search (O(1))
const map = new Map([[1, 'a'], [3, 'b']]);
console.log(map.has(3)); // Check the hash table directly, as fast as lightning!
```

2. **Asynchronous processing time-consuming operation**
```typescript
// Counterexample: Blocking the main thread
function loads large file() {
const data = readBigFile('data.bin'); // Time-consuming operation
Rendering interface (data); // must wait for the previous completion before execution
}

// Correct solution: Use async/await to liberate the main thread
async function loads large file() {
const data = await readBigFileAsync('data.bin'); // Backstage reading file
Rendering interface (data); // Render the file after reading it, and the interface will not be stuck~
}
```

### 3. Network optimization: "Take high-speed rail" for data
1. **Cache Strategy: Data "Test First and Report"**
- Save locally on the first request:
```typescript
async function get user information() {
const cache = await storage.get('userInfo');
if (cache) return cache; // Check the cache first and respond in seconds!
    
const data = await http.get('https://api/user');
storage.set('userInfo', data); // Save it and use it next time
return data;
}
```

2. **Merge request: Reduce "Er runs"**
Merge multiple small requests into one:
```typescript
// Counterexample: Adjust the interface three times
const order = await getOrder();
const user = await getUser(order.userId);
const address = await getAddress(user.addressId);

// Proper solution: One-time adjustment aggregation interface
const { Order, User, Address } = await getOrderWithAll(Order Id);
```

### 4. Equipment adaptation: "Customized shoe size" for the application
1. **Load resources by device performance**
```typescript
const device performance = await device.getPerformanceLevel(); // Get the device performance level
if (device performance === 'low') {
Load low-definition image ('image_320x240.jpg');
} else {
Load high-definition image ('image_1080p.jpg');
}
```

2. **Avoid over-rendering**
- Use `LazyForEach` to load list items lazyly and render only the content in the screen:
```typescript
List() {
LazyForEach(data list, (item) => {
        Item() { Text(item.name) }
    }, (item) => item.id)
}
```


## Chapter 3: Tool Blessing: Use a "microscope" to find problems

### 1. DevEco Studio: Performance issues "Surface Mirror"
1. **CPU Analysis**
- Open Profiler, run the application, and see which function "eats" the maximum CPU time.
- *Scenario*: It was discovered that the `onDraw()` function takes 40% of its time. After optimizing the drawing logic, the frame rate increased from 20fps to 50fps!

2. **Memory Analysis**
- Use Memory Monitor to view the frequency of object creation and find out the "memory leaker".
- *Case*: The sensor subscription was not cancelled when a page was destroyed, resulting in the page instance being unable to be released, and the memory continued to rise - after unsubscribe, the memory curve became stable in seconds~

### 2. System tools: "Stress Test" of Real Equipment
- **Frame rate monitoring**: Turn on "Show refresh frequency" in the developer option to see if the frame rate is stable at 60fps when sliding the list.
- **Power consumption analysis**: Use the "Battery" module to see how many applications wake up, are they too frequent?Add an anti-shake mechanism and only wake up once in 1 second~


## Chapter 4: Practical Case: The Counterattack from "Turning Speed" to "Speed"

### 1. Comparison before and after image application optimization
| Indicators | Before Optimization | After Optimization |
|--------------|----------------------------|----------------------------|
| Memory usage | 200MB+ (load 100 pictures) | 120MB (same as above) |
| Loading speed | Average 5 seconds/piece | 1.5 seconds/piece (including cache hits) |
| CPU occupation | 50%+ | Below 20% |
| User feedback | "I want to drop my phone when I get stuck" | "Silky like Dove" |

### 2. Key optimization actions
1. **Image compression + cache**
- Use `ImageUtils.compress()` to compress pictures according to the device resolution, such as passing 1080p images to a 720p screen?It doesn't exist!
- Use `LocalStorage` to save thumbnails, read the local area directly next time, saving traffic and time~

2. **Async loading + lazy rendering**
- Use the `LazyForEach` list to load only the pictures on the screen, and the "Wait and load" off-screen~
- The picture is transitioned with a placeholder. The gray block is displayed first when loading, and then the original image is displayed after "magic" after loading~

3. **Memory leaks are cured**
- When the page is destroyed, manually cancel all timers, network requests, and sensor subscriptions - just like turning off fire and power before going out, eliminating hidden dangers!


## Chapter 5: Guide to avoid pits: "Performance pits" that I have stepped on in those years
1. **Overoptimization**:
- Adding complex animations to unimportant interfaces, causing low-end machines to get stuck - users may not notice this animation at all, but they are irritated and left ~
*Principle*: Priority is given to optimizing high-frequency operations (such as list sliding, page jump), and then adding icing on the cake!

2. **Ignore low-end devices**:
- If you have no problem testing on high-end machines, it will be launched. As a result, users of 1,000 yuan machines have collectively negative reviews - Remember: users will not care about how awesome your code is, they only care about whether it can be used smoothly!
*Practice*: Create a "beggar version" test machine, which is specially designed to simulate low-end environments.

3. **Supercredible "Framework All-purpose"**:
- I think that the automatic performance will be good after using ArkUI, but I wrote a bunch of nested `Stacks` and complex layouts - frameworks are tools, and using them well is really good!
*Suggestions*: If you can use `Stack`, you don't need to use the layout. If you can use `Column`+`Row`, don't have multiple nesting~


## Last chat
Performance optimization is like "fitness" for the application: there may be no changes at the beginning, but if you persist, the application will become "slim and strong"~ Next time I will tell you how to use Hongmeng's distributed capabilities to do "cross-device performance collaboration", such as allowing mobile phones and tablets to team up to render complex interfaces - remember to squat!😉

(Please indicate the source and original author Xiao L when reprinting. Violators... Let your application card for 0.5 seconds per day! Just kidding~)
