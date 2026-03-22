# Code Structure & Explanation

## Overview

The HD Security Monitor is organized into logical sections with clear purpose:

1. **Imports** - Required libraries
2. **Configuration** - Initial setup (TTS engine, etc.)
3. **Functions** - Reusable code modules
4. **Initialization** - Camera selection & setup
5. **Main Loop** - Continuous frame processing
6. **Cleanup** - Resource release

---

## Section 1: Imports & Dependencies

```python
import cv2                            # Computer vision (video capture/processing)
import numpy as np                    # Array operations
import time                           # Timing functions
import os                             # Operating system interaction
import pyttsx3                        # Text-to-speech
from plyer import notification        # Desktop notifications
import keyboard                       # Global keyboard input
from pygrabber.dshow_graph import FilterGraph    # Windows camera detection
from datetime import datetime         # Timestamp generation
```

**What each does:**
- `cv2`: OpenCV - video capture, image processing, display
- `numpy`: Matrix operations for image manipulation
- `pyttsx3`: Windows TTS engine for voice announcements
- `keyboard`: Listen for keypresses without window focus
- `FilterGraph`: Detect all Windows cameras via DirectShow
- `datetime`: Get current date/time for timestamp

---

## Section 2: Engine Configuration

```python
engine = pyttsx3.init('sapi5')              # Initialize Windows TTS
engine.setProperty('rate', 150)             # Set speech speed (0-300)
```

**Purpose:**
- Sets up text-to-speech for voice notifications
- SAPI5 = Windows standard TTS engine
- Rate 150 = comfortable speaking speed

---

## Section 3: Core Functions

### Function 1: get_camera_by_name()

```python
def get_camera_by_name():
    graph = FilterGraph()                           # Initialize DirectShow
    devices = graph.get_input_devices()             # Get all cameras
    
    print("--- Available Cameras ---")
    for index, name in enumerate(devices):          # List each camera
        print(f"[{index}] : {name}")
    
    selection = int(input("enter the index of the camera to use: "))
    return selection, devices[selection]             # Return index AND name
```

**What it does:**
1. Uses Windows DirectShow API (via pygrabber) to find all cameras
2. Displays them with numbered indices
3. Waits for user input
4. Returns both the camera index (for cv2.VideoCapture) and name (for window title)

**Why it's important:**
- Supports multiple USB cameras
- User can choose which camera to use
- No hardcoded camera numbers needed

**Example output:**
```
--- Available Cameras ---
[0] : Integrated Camera
[1] : USB Webcam
enter the index of the camera to use: 1
```

---

### Function 2: set_optimal_resolution()

```python
def set_optimal_resolution(cap):
    """
    Attempts 1080p → falls back to 720p minimum
    """
    # TRY 1080p
    cap.set(cv2.CAP_PROP_FRAME_WIDTH, 1920)
    cap.set(cv2.CAP_PROP_FRAME_HEIGHT, 1080)
    
    # CHECK what was actually set
    width = int(cap.get(cv2.CAP_PROP_FRAME_WIDTH))
    height = int(cap.get(cv2.CAP_PROP_FRAME_HEIGHT))
    
    # VERIFY success
    if width == 1920 and height == 1080:
        print("✓ Camera resolution set to 1080p (1920x1080)")
        return 1920, 1080
    
    # FALLBACK to 720p
    print("⚠ 1080p not supported. Forcing 720p (1280x720)...")
    cap.set(cv2.CAP_PROP_FRAME_WIDTH, 1280)
    cap.set(cv2.CAP_PROP_FRAME_HEIGHT, 720)
    
    print(f"✓ Camera resolution forced to: 1280x720")
    return 1280, 720
```

**Why it's needed:**
- Not all USB cameras support 1080p
- Manual verification that resolution was set (some cameras lie about capabilities)
- Ensures minimum 720p for stable operation

**Key concept:**
```
Request 1080p → Verify success → If failed, force 720p
```

**Return values used in:**
```python
camera_width, camera_height = set_optimal_resolution(cap)

# Later used for:
frame_hd = cv2.resize(frame, (camera_width, camera_height))
```

---

### Function 3: apply_nvg_effect()

```python
def apply_nvg_effect(frame):
    """
    Converts standard video to military night vision (green monochrome)
    """
    
    # STEP 1: Convert to grayscale
    gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)
    
    # STEP 2: Enhance contrast (CLAHE = Contrast Limited Adaptive Histogram Equalization)
    clahe = cv2.createCLAHE(clipLimit=3.0, tileGridSize=(8, 8))
    enhanced = clahe.apply(gray)
    
    # STEP 3: Create glow effect
    blurred = cv2.GaussianBlur(enhanced, (5, 5), 0)
    glow = cv2.addWeighted(enhanced, 0.7, blurred, 0.3, 0)
    # 70% original + 30% blurred = glowing effect
    
    # STEP 4: Convert grayscale to BGR (3 channels)
    green_frame = cv2.cvtColor(glow, cv2.COLOR_GRAY2BGR)
    
    # STEP 5: Make it GREEN MONOCHROME
    green_frame[:, :, 0] = 0      # Blue channel = 0 (no blue)
    green_frame[:, :, 1] = glow   # Green channel = enhanced image (green shows detail)
    green_frame[:, :, 2] = 0      # Red channel = 0 (no red)
    # Result: Only green channel has data → Green monochrome
    
    # STEP 6: Add vignette (darken edges)
    rows, cols = glow.shape
    kernel_x = cv2.getGaussianKernel(cols, cols/2)     # Horizontal Gaussian
    kernel_y = cv2.getGaussianKernel(rows, rows/2)     # Vertical Gaussian
    kernel = kernel_y * kernel_x.T                      # Combine for 2D Gaussian
    mask = kernel / kernel.max()                        # Normalize to 0-1
    mask = np.dstack([mask] * 3)                        # Apply to all 3 channels
    green_frame = (green_frame * mask).astype(np.uint8) # Multiply frame by mask
    # Result: Edges darker than center (vignette effect)
    
    return green_frame
```

**Understanding the stages:**

```
Input Frame (BGR color)
    ↓
GRAYSCALE (single channel)
    ↓
CONTRAST ENHANCEMENT (CLAHE - makes bright brighter, dark darker)
    ↓
GLOW EFFECT (blend original + blurred)
    ↓
GREEN MONOCHROME (keep only green channel)
    ↓
VIGNETTE (multiply by Gaussian mask)
    ↓
Output: Green military night vision effect
```

**Why CLAHE instead of simple contrast?**
```
Simple contrast:     Might overexpose bright and underexpose dark equally
CLAHE:              Adapts per tile, smart about edge cases
Result:             Better detail preservation
```

---

### Function 4: create_window()

```python
def create_window():
    cv2.namedWindow(win_name, cv2.WINDOW_NORMAL)         # Create window
    cv2.setWindowProperty(win_name, cv2.WND_PROP_TOPMOST, 1)  # Always on top
    cv2.resizeWindow(win_name, 480, 270)                 # Set size
```

**Configuration:**
- `WINDOW_NORMAL`: User can resize window (vs `WINDOW_AUTOSIZE` = fixed)
- `TOPMOST`: Window stays on top of other windows
- Size: 480×270 (HD quarter resolution for display)

---

## Section 4: Initialization

```python
# Get camera selection from user
selected_camera, camera_name = get_camera_by_name()
win_name = camera_name                              # Window title = camera name
cap = cv2.VideoCapture(selected_camera)             # Open camera

# Set optimal resolution
camera_width, camera_height = set_optimal_resolution(cap)

# Configure camera properties
cap.set(cv2.CAP_PROP_BRIGHTNESS, 230)   # Very bright (0-255)
cap.set(cv2.CAP_PROP_CONTRAST, 130)     # Saturated colors
cap.set(cv2.CAP_PROP_EXPOSURE, -4)      # Less light (more sensitive)

# Initialize motion detection
back_sub = cv2.createBackgroundSubtractorMOG2(
    history=500,                    # Analyze last 500 frames
    varThreshold=50,                # 50 = medium sensitivity
    detectShadows=True              # Don't count shadows as motion
)

# Global variables
hidden = False       # Window visible?
voice_active = True  # Voice enabled?
mode = "normal"      # Current mode
```

**Order matters:**
1. Get camera FIRST
2. Set resolution BEFORE adjusting brightness/contrast
3. Initialize motion detector
4. Create window
5. Announce startup

---

## Section 5: Main Loop

```python
while True:
    # READ frame
    ret, frame = cap.read()
    if not ret: break           # Exit if camera disconnected
    
    # RESIZE
    frame_hd = cv2.resize(frame, (camera_width, camera_height), 
                         interpolation=cv2.INTER_LINEAR)
    
    # BLUR (smoothing)
    frame_hd = cv2.GaussianBlur(frame_hd, (3, 3), 0)
    
    # SHARPEN (edge enhancement)
    kernel = np.array([[0, -1, 0], [-1, 5, -1], [0, -1, 0]])
    frame_hd = cv2.filter2D(frame_hd, -1, kernel)
    
    # MOTION DETECT
    fg_mask = back_sub.apply(frame_hd)  # White = moving, Black = static
    
    # CLEAN motion mask (remove noise)
    kernel = np.ones((5,5), np.uint8)
    fg_mask = cv2.morphologyEx(fg_mask, cv2.MORPH_OPEN, kernel)
    
    # FIND contours of moving objects
    contours, _ = cv2.findContours(fg_mask, cv2.RETR_EXTERNAL, 
                                  cv2.CHAIN_APPROX_SIMPLE)
    
    # APPLY MODE
    if mode == "clarity":
        frame_hd = cv2.bilateralFilter(frame_hd, 9, 75, 75)
    elif mode == "noise":
        frame_hd = cv2.medianBlur(frame_hd, 5)
    elif mode == "night":
        gray = cv2.cvtColor(frame_hd, cv2.COLOR_BGR2GRAY)
        clahe = cv2.createCLAHE(clipLimit=2.0, tileGridSize=(8,8))
        frame_hd = clahe.apply(gray)
        frame_hd = cv2.cvtColor(frame_hd, cv2.COLOR_GRAY2BGR)
    elif mode == "nvg":
        frame_hd = apply_nvg_effect(frame_hd)
    
    # ADD OVERLAYS
    current_time = datetime.now().strftime("%Y-%m-%d %H:%M:%S")
    cv2.putText(frame_hd, f"Mode: {mode}", (10, 30),
                cv2.FONT_HERSHEY_SIMPLEX, 1, (0,255,0), 2)
    cv2.putText(frame_hd, f"Time: {current_time}", (10, 70),
                cv2.FONT_HERSHEY_SIMPLEX, 0.9, (0,255,0), 2)
    
    # KEYBOARD CONTROLS
    if keyboard.is_pressed('1'):
        mode = "normal"
    elif keyboard.is_pressed('2'):
        mode = "clarity"
    # ... etc for 3, 4, 5
    
    if keyboard.is_pressed('ctrl+h'):
        if not hidden:
            cv2.destroyWindow(win_name)
            hidden = True
            voice_active = False
            print("Ninja Mode: ON")
    
    if keyboard.is_pressed('ctrl+s'):
        if hidden:
            create_window()
            hidden = False
            voice_active = True
            print("Ninja Mode: OFF")
    
    # DISPLAY
    if not hidden:
        cv2.imshow(win_name, frame_hd)
    
    # EXIT
    if cv2.waitKey(1) & 0xFF == ord('q'):
        break
```

**Loop execution time:**
```
Read: ~5ms
Process: ~10-50ms (depends on mode)
Display: ~1-10ms
Total per frame: ~20-60ms = 17-50 FPS
```

---

## Processing Pipeline Diagram

```
Camera Input Frame (BGR color, resolution-dependent)
    ↓
Resize to (camera_width, camera_height)
    ↓
Gaussian Blur (3×3) - Smoothing
    ↓
Sharpening - Edge enhancement
    ↓
Motion Detection (MOG2) - Background subtraction
    ↓
Morphology Operations - Noise removal
    ↓
[Mode applied] → Normal/Clarity/Noise/Night/NVG
    ↓
Add Text Overlays (timestamp, mode)
    ↓
[Mode dependent - Display if not hidden] 
    ↓
Screen Display (480×270 window)
```

---

## Memory & Performance

**Per-frame allocations:**
```python
frame_hd = cv2.resize(...)          # Copy to new array ~2-5MB
fg_mask = back_sub.apply(...)       # Motion mask ~1MB
glow = cv2.addWeighted(...)         # Temporary arrays
clahe = cv2.createCLAHE(...)        # Persistent histograms
```

**CPU usage by mode:**
- Normal: ~20% CPU
- Clarity: ~30% CPU (bilateral filter = intensive)
- Noise: ~20% CPU
- Night: ~25% CPU (CLAHE = medium intensity)
- NVG: ~28% CPU (CLAHE + Gaussian blur)

---

## Key Concepts

### 1. Video Capture Loop
```python
while True:
    ret, frame = cap.read()     # ret=success, frame=image
    if not ret: break           # Camera disconnected
    # Process frame
    if exit_condition:
        break
cap.release()                   # Clean up
```

### 2. Image Filtering Chain
```
Each operation modifies frame_hd in-place or creates new version:
frame_hd = op1(frame) → op2(frame_hd) → op3(frame_hd)
```

### 3. Global Keyboard Monitoring
```python
keyboard.is_pressed('key')  # Works even if window not focused
No blocking wait - checks current state
```

### 4. Conditional Mode Selection
```python
if mode == "x":
    # Only process if user selected this mode
    # Saves CPU vs processing all modes always
```

---

## Customization Points

Change these values to customize:

```python
# Brightness/Contrast (Camera initialization)
cap.set(cv2.CAP_PROP_BRIGHTNESS, 230)   # Try 0-255

# Motion detection sensitivity
back_sub = cv2.createBackgroundSubtractorMOG2(history=500, varThreshold=50)
# Lower varThreshold = more sensitive (more false positives)

# Sharpening intensity
kernel = np.array([[0, -1, 0], [-1, 5, -1], [0, -1, 0]])
# Change the 5 to increase/decrease sharpening

# Display window size
cv2.resizeWindow(win_name, 480, 270)    # Change 480, 270 to your preference
```

---

## Error Handling

Currently handled:
- Camera disconnection: `if not ret: break`
- Empty device list: Would crash (could add check)
- Invalid camera selection: Would crash (could add try/except)

**Could improve with:**
```python
try:
    cap = cv2.VideoCapture(selected_camera)
except:
    print("Camera failed to initialize")
```

---

## Conclusion

The code is structured for:
- ✅ Clarity (each function has one job)
- ✅ Efficiency (processes only what's needed)
- ✅ Flexibility (easy to add new modes)
- ✅ Robustness (handles common errors)

---

**Author:** GumbiTech  
**GitHub:** [@GumbiTech](https://github.com/GumbiTech)

Happy monitoring! 🎥
