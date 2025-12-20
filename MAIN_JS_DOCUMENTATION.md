# Main.js Documentation - Complete Code Analysis

## Table of Contents
1. [Overview](#overview)
2. [Dependencies & Imports](#dependencies--imports)
3. [Audio System](#audio-system)
4. [Scene Setup](#scene-setup)
5. [Modal System](#modal-system)
6. [Loading Screen & Intro Animation](#loading-screen--intro-animation)
7. [Loaders & Textures](#loaders--textures)
8. [3D Model Loading](#3d-model-loading)
9. [Raycaster & Interaction System](#raycaster--interaction-system)
10. [Event Listeners](#event-listeners)
11. [Render Loop & Animations](#render-loop--animations)

---

## Overview

This is the main JavaScript file for a 3D interactive portfolio room built with Three.js. It creates an immersive 3D environment where users can interact with various objects, toggle themes (day/night), play piano keys, and navigate through modals (Work, About, Contact).

**Total Lines:** 1867  
**Main Technologies:** Three.js, GSAP (animations), Howler.js (audio)

---

## Dependencies & Imports
**Lines: 1-13**

### External Libraries
- **GSAP** - Animation library for smooth transitions
- **Howler.js** - Audio management library
- **Three.js** - 3D graphics library

### Three.js Components
- `OrbitControls` - Camera controls for user navigation
- `DRACOLoader` - Compressed 3D model loader
- `GLTFLoader` - GLTF/GLB 3D model loader

### Custom Shaders
- `smokeVertexShader` & `smokeFragmentShader` - For coffee smoke effect
- `themeVertexShader` & `themeFragmentShader` - For day/night theme transitions

---

## Audio System
**Lines: 15-99**

### Background Music
**Lines: 17-51**

- **File:** `/audio/music/cosmic_candy.ogg`
- **Volume:** 1 (full volume)
- **Loop:** Yes
- **Fade Duration:** 500ms

**Key Variables:**
- `isMusicFaded` - Tracks if music is currently faded
- `MUSIC_FADE_TIME` - 500ms fade duration
- `PIANO_TIMEOUT` - 2000ms delay before music fades back in after piano play

**Functions:**
- `fadeOutBackgroundMusic()` - Fades music to 0 when piano is played
- `fadeInBackgroundMusic()` - Restores music volume after piano timeout

### Piano Sounds
**Lines: 53-89**

- **24 Piano Keys** mapped from C1 to B2 (2 octaves)
- Each key has a corresponding sound file in `/audio/sfx/piano/`
- **Volume:** 0.5
- **Preload:** true (all sounds loaded at start)

**Piano Key Mapping:**
```javascript
pianoKeyMap = {
  C1_Key: "Key_24",
  "C#1_Key": "Key_23",
  // ... continues through B2_Key: "Key_1"
}
```

### Button Sounds
**Lines: 91-98**

- **Click Sound:** `/audio/sfx/click/bubble.ogg`
- **Volume:** 0.5
- Used for UI button interactions

---

## Scene Setup
**Lines: 100-170**

### Canvas & Renderer
**Lines: 101-123**

- **Canvas Element:** `#experience-canvas`
- **Renderer:** WebGLRenderer with antialiasing
- **Pixel Ratio:** Capped at 2 for performance

### Camera
**Lines: 110-115**

- **Type:** PerspectiveCamera
- **FOV:** 35 degrees
- **Near Plane:** 0.1
- **Far Plane:** 200

### Orbit Controls
**Lines: 125-136**

**Distance Limits:**
- Min: 5 units
- Max: 38 units

**Angle Limits:**
- Polar Angle: 0 to π/2 (prevents going below ground)
- Azimuth Angle: 0 to π/2 (limits horizontal rotation)

**Damping:** Enabled with factor 0.05 for smooth camera movement

### Initial Camera Position
**Lines: 138-157**

**Mobile (width < 768px):**
- Position: (29.57, 14.02, 31.37)
- Target: (-0.08, 3.31, -0.74)

**Desktop:**
- Position: (17.49, 9.11, 17.85)
- Target: (0.46, 1.97, -0.83)

### Window Resize Handler
**Lines: 159-170**

Updates camera aspect ratio and renderer size on window resize.

---

## Modal System
**Lines: 172-306**

### Modal Elements
**Lines: 173-179**

Three modals are defined:
- **Work Modal** - Portfolio/projects
- **About Modal** - About information
- **Contact Modal** - Contact information

Plus an **overlay** element for background dimming.

### Touch & Click Handling
**Lines: 181-248**

**Touch Prevention Logic:**
- Uses `touchHappened` flag to prevent double-firing on touch devices
- Overlay closes modal on click/touch
- Exit buttons have scale animation (1 → 5 → 1) with GSAP

### Modal Functions

#### `showModal(modal)` - Lines: 252-285
**Purpose:** Opens a modal with animation

**Actions:**
1. Sets modal and overlay display to "block"
2. Disables orbit controls
3. Resets any hovered objects
4. Animates modal from scale 0 to 1 with "back.out" easing
5. Fades in overlay opacity

#### `hideModal(modal)` - Lines: 287-306
**Purpose:** Closes a modal with animation

**Actions:**
1. Re-enables orbit controls
2. Animates modal to scale 0 with "back.in" easing
3. Fades out overlay
4. Sets display to "none" on complete

---

## Loading Screen & Intro Animation
**Lines: 308-788**

### Loading Manager
**Lines: 310-380**

**Elements:**
- `.loading-screen` - Main loading screen
- `.loading-screen-button` - "Enter!" button
- `.no-sound-button` - Option to enter without sound

**Loading Complete (onLoad):**
1. Button changes style and text to "Enter!"
2. Two entry options:
   - **With Sound** - Plays background music
   - **Without Sound** - Mutes all audio

**Entry Actions:**
- Toggles favicons
- Plays reveal animation
- Starts intro animation

### Reveal Animation
**Lines: 382-405**

**Function:** `playReveal()`

**Timeline:**
1. Scales loading screen to 0.5 (1.2s)
2. Moves screen off viewport with 3D rotation (1.2s)
3. Removes loading screen element
4. Triggers intro animation

### Intro Animation
**Lines: 407-788**

**Function:** `playIntroAnimation()`

Animates all room objects from scale 0 to 1 in choreographed sequence.

**Animation Groups:**

1. **Planks & Buttons** (Lines: 416-455)
   - Hanging planks
   - Work, About, Contact buttons
   - Staggered timing with -0.5s to -0.6s offsets

2. **Frames** (Lines: 457-488)
   - 3 picture frames
   - Sequential animation with -0.5s offset

3. **Social Icons** (Lines: 490-524)
   - GitHub, YouTube, Twitter
   - Staggered with -0.5s to -0.6s offsets

4. **Flowers** (Lines: 526-544)
   - Flower decorations
   - Single flower animated (flower4)

5. **Lamp** (Lines: 547-560)
   - Lamp object with 0.2s delay

6. **Slippers** (Lines: 562-585)
   - 2 slippers with sequential animation

7. **Letters** (Lines: 588-714)
   - Name letters (1, 4, 5, 6)
   - Each letter: bounces up 0.3 units, scales in, drops back down
   - Staggered with -0.5s offsets

8. **Piano Keys** (Lines: 716-787)
   - All 24 piano keys
   - Each key bounces up 0.2 units, scales in, drops back
   - Staggered by index * 0.1s
   - **Creates delayed hitboxes** after 1950ms delay

---

## Loaders & Textures
**Lines: 790-936**

### Loaders
**Lines: 791-797**

- **TextureLoader** - For image textures
- **DRACOLoader** - For compressed models (decoder path: `/draco/`)
- **GLTFLoader** - For 3D models, uses DRACOLoader

### Environment Map
**Lines: 799-801**

**Skybox Textures:**
- Located in `textures/skybox/`
- 6 faces: px, nx, py, ny, pz, nz (all .webp format)

### Texture System
**Lines: 803-843**

**Texture Sets:** 4 sets (First, Second, Third, Fourth)

Each set has:
- **Day texture** - `/textures/room/day/[set]_texture_set_day.webp`
- **Night texture** - `/textures/room/night/[set]_texture_set_night.webp`

**Texture Configuration:**
- `flipY: false`
- `colorSpace: THREE.SRGBColorSpace`
- `minFilter: THREE.LinearFilter`
- `magFilter: THREE.LinearFilter`

### Materials

#### Glass Material (Lines: 846-859)
**Type:** MeshPhysicalMaterial

**Properties:**
- Transmission: 1 (fully transparent)
- IOR: 3 (index of refraction)
- Thickness: 0.01
- Uses environment map for reflections

#### White Material (Lines: 861-863)
**Type:** MeshBasicMaterial
- Color: 0xffffff (white)

#### Room Materials (Lines: 865-898)
**Type:** ShaderMaterial (custom theme shader)

**Function:** `createMaterialForTextureSet(textureSet)`

**Uniforms:**
- `uDayTexture1-4` - Day textures for all 4 sets
- `uNightTexture1-4` - Night textures for all 4 sets
- `uMixRatio` - Blend between day/night (0 = day, 1 = night)
- `uTextureSet` - Which texture set to use (1-4)

**Usage:** Allows smooth transition between day and night themes

### Smoke Shader (Lines: 900-923)
**Purpose:** Creates animated smoke effect for coffee cup

**Geometry:**
- PlaneGeometry (1x1, 16x64 segments)
- Translated and scaled to fit coffee cup

**Material:**
- Custom ShaderMaterial
- Uses Perlin noise texture (`/shaders/perlin.png`)
- Uniforms: `uTime`, `uPerlinTexture`
- Double-sided, transparent

**Position:** y = 1.83 (above coffee cup)

### Video Texture (Lines: 925-935)
**Purpose:** Screen display in the room

**Video Element:**
- Source: `/textures/video/Screen.mp4`
- Loop: true
- Muted: true
- Autoplay: true

**Texture:**
- VideoTexture with SRGB color space
- flipY: false

---

## 3D Model Loading
**Lines: 937-1276**

### Model Variables
**Lines: 940-995**

**Declared Variables:**
- `fish` - Animated fish object
- `coffeePosition` - Position for smoke placement
- `hourHand`, `minuteHand` - Clock hands
- `chairTop` - Rotating chair
- `xAxisFans`, `yAxisFans` - Arrays for fan objects
- `plank1`, `plank2` - Hanging planks
- `workBtn`, `aboutBtn`, `contactBtn` - Interactive buttons
- `boba`, `github`, `youtube`, `twitter` - Interactive objects
- `letter1-8` - Name letters
- `C1_Key` through `B2_Key` - 24 piano keys
- `flower1-5` - Flower decorations
- `box1-3` - Box objects
- `lamp` - Lamp object
- `slippers1-2` - Slipper objects
- `egg1-3` - Egg objects
- `frame1-3` - Picture frames

### Configuration Arrays

#### `useOriginalMeshObjects` (Line: 996)
Objects that use their original mesh as hitbox (no separate hitbox created):
- "Bulb"
- "Cactus"
- "Kirby"

#### `objectsNeedingHitboxes` (Line: 998)
Array to store objects that need delayed hitbox creation (after intro animation)

#### `objectsWithIntroAnimations` (Lines: 1000-1058)
List of 58 objects that have intro animations:
- Hanging planks
- Buttons (Work, About, Contact)
- Social icons
- Name letters
- Flowers, boxes, lamp, slippers
- Fish, eggs, frames
- All 24 piano keys

#### `hasIntroAnimation(objectName)` (Lines: 1060-1064)
Helper function to check if an object has intro animation

### Model Loader
**Lines: 1066-1276**

**Model File:** `/models/room4.glb`

**Traversal Logic:**
The loader traverses all meshes in the model and performs different actions based on object names:

#### Special Object Handling

**Fish (Lines: 1069-1076):**
- Adjusts position (+0.04 x, -0.03 z)
- Stores initial position for animation

**Chair Top (Lines: 1077-1080):**
- Stores initial rotation for animation

**Clock Hands (Lines: 1082-1090):**
- Stores initial rotation for both hour and minute hands

**Coffee (Lines: 1092-1094):**
- Stores position for smoke placement

**Hover & Key Objects (Lines: 1096-1102):**
- Stores initial scale, position, and rotation
- Used for hover animations and piano key presses

#### Object Initialization (Lines: 1105-1197)
**Pattern:** Objects with intro animations start at scale (0,0,0)

**Examples:**
- Planks: Different initial scales
- Buttons: All start at (0,0,0)
- Social icons: All start at (0,0,0)
- Letters, flowers, boxes, etc.: All start at (0,0,0)

#### Piano Keys (Lines: 1198-1207)
- Uses `eval()` to dynamically assign variables
- Converts key names (e.g., "C#1_Key" → "Cs1_Key")
- All start at scale (0,0,0)
- Stores initial position

#### Material Assignment (Lines: 1209-1243)

**Water (Lines: 1209-1215):**
- MeshBasicMaterial
- Color: 0x558bc8 (blue)
- Transparent with 0.4 opacity

**Glass (Lines: 1216-1217):**
- Uses glassMaterial (defined earlier)

**Bubble (Lines: 1218-1219):**
- Uses whiteMaterial

**Screen (Lines: 1220-1225):**
- MeshBasicMaterial with videoTexture
- 0.9 opacity

**Textured Objects (Lines: 1227-1242):**
- Matches object name to texture set (First, Second, Third, Fourth)
- Applies corresponding room material
- **Fans:** Separated into xAxisFans and yAxisFans arrays

#### Raycaster Setup (Lines: 1245-1263)
**For objects with "Raycaster" in name:**

**With Intro Animation:**
- Sets original scale to (1,1,1)
- Adds to `objectsNeedingHitboxes` array
- Hitbox created later after animation

**Without Intro Animation:**
- Creates hitbox immediately with `createStaticHitbox()`
- Adds to scene if separate hitbox created
- Adds to `raycasterObjects` array
- Maps hitbox to original object in `hitboxToObjectMap`

#### Smoke Positioning (Lines: 1267-1273)
If coffee position found, positions smoke above it (+0.2 y offset)

#### Scene Addition (Line: 1275)
Adds entire loaded model to scene

---

## Raycaster & Interaction System
**Lines: 1278-1560**

### Setup
**Lines: 1280-1293**

**Variables:**
- `raycasterObjects` - Array of objects that can be raycasted
- `currentIntersects` - Currently intersected objects
- `currentHoveredObject` - Currently hovered object
- `raycaster` - THREE.Raycaster instance
- `pointer` - 2D vector for mouse/touch position
- `hitboxToObjectMap` - Maps hitboxes to original objects

**Social Links (Lines: 1284-1288):**
- GitHub: Portfolio repository
- YouTube: Demo video
- Twitter: Twitter profile

### Helper Functions

#### `shouldUseOriginalMesh(objectName)` (Lines: 1295-1299)
Checks if object should use original mesh instead of separate hitbox

#### `createStaticHitbox(originalObject)` (Lines: 1301-1383)
**Purpose:** Creates invisible hitbox for raycasting

**For Original Mesh Objects:**
- Returns the original object itself
- Stores initial scale, position, rotation

**For Other Objects:**
1. Stores initial transformations
2. Temporarily restores scale if currently at 0
3. Calculates bounding box
4. Creates BoxGeometry hitbox (1.1x width, 1.75x height, 1.1x depth)
5. Creates invisible MeshBasicMaterial
6. Positions hitbox at object center
7. Stores reference to original object
8. Special rotation for headphones

#### `createDelayedHitboxes()` (Lines: 1385-1398)
**Purpose:** Creates hitboxes for objects after intro animation completes

**Process:**
1. Iterates through `objectsNeedingHitboxes`
2. Creates hitbox for each
3. Adds to scene if separate hitbox
4. Adds to `raycasterObjects` array
5. Maps hitbox to original object
6. Clears the array

**Called:** After piano keys animation (1950ms delay)

### Interaction Handler

#### `handleRaycasterInteraction()` (Lines: 1400-1456)
**Purpose:** Handles click/touch on raycasted objects

**Button Clicks (Lines: 1405-1407):**
- Plays click sound for any button

**Piano Keys (Lines: 1409-1436):**
1. Clears existing piano debounce timer
2. Fades out background music
3. Plays corresponding piano sound
4. Animates key rotation (tilts forward then back)
5. Sets timer to fade music back in after 2000ms

**Social Links (Lines: 1438-1446):**
- Opens link in new window with security attributes
- GitHub, YouTube, Twitter

**Modal Buttons (Lines: 1448-1454):**
- Work Button → Opens work modal
- About Button → Opens about modal
- Contact Button → Opens contact modal

### Hover Animation

#### `playHoverAnimation(objectHitbox, isHovering)` (Lines: 1458-1560)
**Purpose:** Animates objects on hover

**Parameters:**
- `objectHitbox` - The hitbox being hovered
- `isHovering` - true for hover start, false for hover end

**General Behavior:**
- Kills existing GSAP tweens
- Default scale: 1.4x
- Fish scale: 1.2x (smaller)

**Coffee Special Effect (Lines: 1465-1484):**
- Also scales smoke when hovering coffee

**Hover Start (isHovering = true):**

1. **Scale Animation (Lines: 1492-1498):**
   - Scales to 1.4x (or 1.2x for fish)
   - Duration: 0.5s
   - Easing: "back.out(2)"

2. **Rotation Animations (Lines: 1500-1518):**
   - **About Button:** Rotates backward (-π/10)
   - **Contact/Work/Social Icons:** Rotate forward (+π/10)

3. **Position Animations (Lines: 1520-1526):**
   - **Boba & Letters:** Move up 0.2 units

**Hover End (isHovering = false):**

1. **Scale Reset (Lines: 1529-1535):**
   - Returns to initial scale
   - Duration: 0.3s

2. **Rotation Reset (Lines: 1537-1550):**
   - Returns to initial rotation
   - Applies to buttons and social icons

3. **Position Reset (Lines: 1552-1558):**
   - Returns to initial position
   - Applies to boba and letters

---

## Event Listeners
**Lines: 1562-1760**

### Mouse & Touch Events

#### Mouse Move (Lines: 1562-1566)
- Resets `touchHappened` flag
- Converts mouse position to normalized device coordinates (-1 to 1)

#### Touch Start (Lines: 1568-1577)
- Prevents default if modal is closed
- Converts touch position to normalized coordinates
- Passive: false (allows preventDefault)

#### Touch End (Lines: 1579-1587)
- Prevents default if modal is closed
- Calls `handleRaycasterInteraction()`

#### Click (Line: 1589)
- Calls `handleRaycasterInteraction()`

### UI Controls

#### DOM Elements (Lines: 1592-1597)
- `themeToggleButton` - Day/night toggle
- `muteToggleButton` - Sound on/off toggle
- `sunSvg`, `moonSvg` - Theme icons
- `soundOffSvg`, `soundOnSvg` - Mute icons

### Mute Toggle

#### `updateMuteState(muted)` (Lines: 1599-1610)
**Purpose:** Updates audio state

**Actions:**
- Sets background music volume (0 or 1)
- Mutes/unmutes button sounds
- Mutes/unmutes all piano sounds

#### `handleMuteToggle(e)` (Lines: 1612-1650)
**Purpose:** Toggles mute state with animation

**Process:**
1. Toggles `isMuted` flag
2. Updates mute state
3. Plays click sound
4. Starts background music if not playing
5. Animates button (rotate -45°, scale 5x)
6. Swaps sound icon (on ↔ off)
7. Animates back to normal

**Event Listeners (Lines: 1652-1669):**
- Click event (desktop)
- Touch end event (mobile)

### Theme Toggle

#### `toggleFavicons()` (Lines: 1672-1691)
**Purpose:** Swaps favicon based on theme

**Updates:**
- favicon-96x96.png
- favicon.svg
- favicon.ico
- apple-touch-icon.png
- site.webmanifest

**Paths:** `/media/[theme]-favicon/[file]`

#### `handleThemeToggle(e)` (Lines: 1695-1741)
**Purpose:** Toggles day/night theme

**Process:**
1. Toggles favicons
2. Swaps body class (dark-theme ↔ light-theme)
3. Toggles `isNightMode` flag
4. Plays click sound
5. Animates button (rotate 45°, scale 5x)
6. Swaps theme icon (sun ↔ moon)
7. Animates back to normal
8. **Transitions room materials** (uMixRatio: 0 → 1 or 1 → 0)
   - Duration: 1.5s
   - Easing: "power2.inOut"

**Event Listeners (Lines: 1743-1760):**
- Click event (desktop)
- Touch end event (mobile)

---

## Render Loop & Animations
**Lines: 1762-1867**

### Clock
**Line: 1763**

THREE.Clock instance for tracking elapsed time

### Clock Hands Update

#### `updateClockHands()` (Lines: 1765-1779)
**Purpose:** Updates clock to show real time

**Process:**
1. Gets current time
2. Calculates minute angle (includes seconds for smooth movement)
3. Calculates hour angle (includes minutes for smooth movement)
4. Rotates hands on X-axis (negative for correct direction)

**Formula:**
- Minute: `(minutes + seconds/60) * (2π/60)`
- Hour: `(hours + minutes/60) * (2π/12)`

### Main Render Loop

#### `render(timestamp)` (Lines: 1781-1864)
**Purpose:** Main animation loop called every frame

**Updates:**

1. **Smoke Shader (Line: 1785):**
   - Updates `uTime` uniform with elapsed time
   - Creates animated smoke effect

2. **Orbit Controls (Line: 1788):**
   - Updates camera controls (damping, etc.)

3. **Clock Hands (Line: 1791):**
   - Updates to current time

4. **Fan Rotation (Lines: 1794-1800):**
   - **X-axis fans:** Rotate on X-axis (-0.04 rad/frame)
   - **Y-axis fans:** Rotate on Y-axis (-0.04 rad/frame)

5. **Chair Animation (Lines: 1803-1813):**
   - Oscillating rotation on Y-axis
   - **Base Amplitude:** π/8
   - **Frequency:** 0.5
   - **Damping:** Reduces amplitude at extremes (1 - |sin| * 0.3)
   - Creates natural swivel motion

6. **Fish Animation (Lines: 1816-1822):**
   - Vertical bobbing motion
   - **Amplitude:** 0.12 units
   - **Frequency:** 0.0015
   - **Damping:** Reduces amplitude at extremes (1 - |sin| * 0.1)
   - Creates floating effect

7. **Raycaster (Lines: 1825-1859):**
   **Only when modal is closed:**
   
   a. **Update Raycaster (Line: 1826):**
      - Sets raycaster from camera and pointer position
   
   b. **Get Intersections (Line: 1829):**
      - Finds all objects intersecting with ray
   
   c. **Hover Detection (Lines: 1833-1846):**
      - If intersecting object has "Hover" in name:
        - If different from current hovered object:
          - Unhover previous object
          - Hover new object
   
   d. **Cursor Update (Lines: 1847-1851):**
      - "Pointer" objects: cursor = pointer
      - Other objects: cursor = default
   
   e. **No Intersection (Lines: 1852-1858):**
      - Unhover current object
      - Reset cursor to default

8. **Render Scene (Line: 1861):**
   - Renders scene with camera

9. **Request Next Frame (Line: 1863):**
   - Calls render again on next frame

### Start Render Loop
**Line: 1866**

Initial call to `render()` to start the animation loop

---

## Key Concepts Summary

### Where Models Are Used

**3D Model File:** `/models/room4.glb`
- **Loaded at:** Line 1066
- **Contains:** Entire room with all objects (furniture, decorations, interactive elements)
- **Processing:** Lines 1067-1275 (traverses and configures all meshes)

**Model Objects Include:**
- Room structure (walls, floor, ceiling)
- Furniture (chair, desk, lamp, etc.)
- Interactive elements (buttons, piano, social icons)
- Decorations (flowers, frames, eggs, slippers, etc.)
- Animated objects (fish, clock hands, fans, chair)

### Where Modals Are Used

**Modal System:** Lines 172-306

**Three Modals:**
1. **Work Modal** - Opened by clicking "My Work" button
2. **About Modal** - Opened by clicking "About" button  
3. **Contact Modal** - Opened by clicking "Contact" button

**Modal Triggers:**
- Lines 1448-1454 in `handleRaycasterInteraction()`
- Clicking corresponding 3D buttons in the scene

**Modal Features:**
- Overlay background (dims scene)
- Scale animation (0 → 1 on open, 1 → 0 on close)
- Disables camera controls when open
- Can be closed by clicking overlay or exit button

### Shader Usage

**Two Shader Systems:**

1. **Smoke Shader (Lines: 900-923):**
   - Creates animated smoke rising from coffee cup
   - Uses Perlin noise for organic movement
   - Updated every frame with elapsed time

2. **Theme Shader (Lines: 865-898):**
   - Blends between day and night textures
   - Applied to room materials (4 texture sets)
   - Controlled by `uMixRatio` uniform (0-1)
   - Smoothly transitions when theme is toggled

### Animation Systems

**GSAP Animations:**
- Intro animations (objects appearing)
- Hover animations (scale, rotation, position)
- Button click animations
- Modal open/close animations
- Theme/mute toggle animations

**Three.js Animations (in render loop):**
- Smoke shader (time-based)
- Fan rotation (continuous)
- Chair swivel (oscillating)
- Fish bobbing (oscillating)
- Clock hands (real-time)

### Interaction Flow

1. **User moves mouse/touches screen**
2. **Pointer position updates** (normalized coordinates)
3. **Raycaster checks intersections** (every frame)
4. **If hovering over "Hover" object:**
   - Plays hover animation
   - Changes cursor if "Pointer" object
5. **If clicking/touching:**
   - Calls `handleRaycasterInteraction()`
   - Performs action based on object type:
     - Piano keys → Play sound
     - Buttons → Open modal
     - Social icons → Open link

### Audio Management

**Three Audio Types:**
1. **Background Music** - Loops continuously, fades when piano played
2. **Piano Sounds** - 24 individual sounds, triggers music fade
3. **Button Sounds** - Click sound for UI interactions

**Mute System:**
- Global `isMuted` flag
- Affects all audio sources
- Persists through interactions

---

## File Structure Summary

```
Lines 1-13:     Imports
Lines 15-99:    Audio Setup
Lines 100-170:  Scene Setup
Lines 172-306:  Modal System
Lines 308-788:  Loading & Intro Animation
Lines 790-936:  Loaders & Textures
Lines 937-1276: Model Loading
Lines 1278-1560: Raycaster & Interactions
Lines 1562-1760: Event Listeners
Lines 1762-1867: Render Loop
```

---

## Performance Considerations

1. **Pixel Ratio Capped:** Max 2 for performance
2. **Texture Filtering:** LinearFilter for smooth appearance
3. **DRACO Compression:** Models compressed for faster loading
4. **Preloaded Audio:** All sounds loaded at start
5. **Efficient Raycasting:** Only when modal closed
6. **GSAP Tween Killing:** Prevents animation conflicts
7. **Delayed Hitbox Creation:** Only after intro animation

---

## Common Patterns

### Object Initialization Pattern
```javascript
if (child.name.includes("ObjectName")) {
  objectVariable = child;
  child.scale.set(0, 0, 0); // For intro animation
  child.userData.initialPosition = new THREE.Vector3().copy(child.position);
}
```

### Animation Pattern
```javascript
gsap.to(object.property, {
  value: targetValue,
  duration: seconds,
  ease: "easingFunction",
  onComplete: callback
});
```

### Raycaster Pattern
```javascript
raycaster.setFromCamera(pointer, camera);
currentIntersects = raycaster.intersectObjects(raycasterObjects);
if (currentIntersects.length > 0) {
  // Handle interaction
}
```

---

**End of Documentation**

*Generated: 2025-12-17*  
*File: main.js (1867 lines)*
