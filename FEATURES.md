# Features Overview

## 🎯 Core Features

### 1. Multi-USB Webcam Support

**What it does:**
- Automatically detects ALL USB cameras connected to your PC
- Displays them with indices (0, 1, 2...)
- Lets you select which camera to use

**How it works:**
```python
def get_camera_by_name():
    graph = FilterGraph()  # Uses Windows DirectShow
    devices = graph.get_input_devices()  # Gets all cameras
    
    # Lists all cameras:
    # [0] : Integrated Camera
    # [1] : USB Webcam - HD
    # [2] : External Security Camera
```

**Why it's useful:**
- Run multiple monitoring sessions on different cameras
- No hardcoding camera numbers
- Works with any USB webcam

---

### 2. Smart Resolution Management

**What it does:**
- Tries to use **1080p (1920×1080)** for maximum quality
- Automatically falls back to **720p (1280×720)** if 1080p isn't supported
- **Never goes below 720p** for stable performance

**Why it's useful:**
- Works with old and new cameras
- Best possible quality for each camera
- Prevents unstable extremely low resolutions

**Code:**
```python
def set_optimal_resolution(cap):
    # Try 1080p
    cap.set(cv2.CAP_PROP_FRAME_WIDTH, 1920)
    cap.set(cv2.CAP_PROP_FRAME_HEIGHT, 1080)
    
    # Check if set successfully
    width = int(cap.get(cv2.CAP_PROP_FRAME_WIDTH))
    height = int(cap.get(cv2.CAP_PROP_FRAME_HEIGHT))
    
    if width == 1920 and height == 1080:
        return 1920, 1080
    else:
        # Force 720p if 1080p failed
        return 1280, 720
```

---

## 🎨 Display Modes (5 Total)

### Mode 1: Normal
**Best for:** Standard security monitoring

Features:
- Bilinear interpolation (smooth scaling)
- Gaussian blur smoothing
- Gentle sharpening
- Live timestamp display

**Technical:**
- Uses INTER_LINEAR interpolation (3-tap filter)
- GaussianBlur(3,3) for smoothing
- Sharpening kernel: `[[0,-1,0], [-1,5,-1], [0,-1,0]]`

**Use case:** 24/7 monitoring, general surveillance

---

### Mode 2: Clarity
**Best for:** Well-lit environments, detailed monitoring

Features:
- Bilateral filtering
- Removes noise while keeping edges sharp
- Very smooth, artifact-free
- High computational cost

**Technical:**
```python
cv2.bilateralFilter(frame, 9, 75, 75)
# Params: diameter=9, color_sigma=75, space_sigma=75
```

**Use case:** Monitoring offices, indoors where you need detail

---

### Mode 3: Noise
**Best for:** Noisy camera feeds, low-quality sensors

Features:
- Median blur filtering
- Perfect for salt-and-pepper noise
- Removes noise while preserving edges
- Fast processing

**Technical:**
```python
cv2.medianBlur(frame, 5)  # 5x5 median kernel
```

**Use case:** Old cameras, cheap USB webcams, noisy sensors

---

### Mode 4: Night
**Best for:** Low-light and dark environments

Features:
- CLAHE (Contrast Limited Adaptive Histogram Equalization)
- Enhances dark areas without overexposure
- Better than simple contrast adjustment
- Great for security at night

**Technical:**
```python
clahe = cv2.createCLAHE(clipLimit=2.0, tileGridSize=(8,8))
# ClipLimit: 2.0 (prevents over-brightening)
# TileGridSize: 8×8 tiles for even processing
```

**Use case:** Night security, outdoor monitoring, low-light conditions

---

### Mode 5: NVG (Night Vision Goggles)
**Best for:** Tactical monitoring, intimidation factor, unique aesthetics

Features:
- **Military green monochrome** color scheme
- **High contrast** for visibility
- **Glowing highlights** effect
- **Vignette** (darkened corners) for authenticity
- **Professional tactical look**

**Technical:**
```python
def apply_nvg_effect(frame):
    # Grayscale conversion
    gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)
    
    # High contrast enhancement (CLAHE)
    clahe = cv2.createCLAHE(clipLimit=3.0, tileGridSize=(8,8))
    enhanced = clahe.apply(gray)
    
    # Glow effect (blur + blend)
    blurred = cv2.GaussianBlur(enhanced, (5,5), 0)
    glow = cv2.addWeighted(enhanced, 0.7, blurred, 0.3, 0)
    
    # Green monochrome conversion
    green_frame = cv2.cvtColor(glow, cv2.COLOR_GRAY2BGR)
    green_frame[:, :, 0] = 0    # No blue
    green_frame[:, :, 1] = glow # Full green
    green_frame[:, :, 2] = 0    # No red
    
    # Vignette effect (dark corners)
    rows, cols = glow.shape
    kernel_x = cv2.getGaussianKernel(cols, cols/2)
    kernel_y = cv2.getGaussianKernel(rows, rows/2)
    kernel = kernel_y * kernel_x.T
    mask = kernel / kernel.max()
    mask = np.dstack([mask] * 3)
    green_frame = (green_frame * mask).astype(np.uint8)
    
    return green_frame
```

**Color Breakdown:**
- Red channel: 0 (off)
- Green channel: Enhanced image (shows detail)
- Blue channel: 0 (off)
- Result: Military green appearance

**Use case:** Cool footage, special occasions, themed monitoring

---

## ⏰ Real-Time Timestamp

**Feature:**
- Displays current date and time on video feed
- Format: `YYYY-MM-DD HH:MM:SS`
- Updates every frame
- Yellow/green text overlay

**Technical:**
```python
from datetime import datetime

current_time = datetime.now().strftime("%Y-%m-%d %H:%M:%S")
cv2.putText(frame_hd, f"Time: {current_time}", (10, 70),
            cv2.FONT_HERSHEY_SIMPLEX, 0.9, (0,255,0), 2)
```

**Why it's useful:**
- Proves when footage was captured
- Essential for security records
- Useful for incident investigation

---

## 🎤 Voice Notifications

**Feature:**
- Text-to-speech announces when system starts
- Says: "system is now online"
- Uses Windows text-to-speech engine (SAPI5)

**Technical:**
```python
import pyttsx3

engine = pyttsx3.init('sapi5')  # Windows TTS
engine.setProperty('rate', 150)  # Speed
engine.say("system is now online.")
engine.runAndWait()
```

**Customization:**
Change the voice speed (0-300):
```python
engine.setProperty('rate', 150)  # Normal speed
engine.setProperty('rate', 250)  # Fast
engine.setProperty('rate', 100)  # Slow
```

---

## 👻 Ninja Mode

**What it does:**
- **Ctrl+H**: Hide window + mute audio
- **Ctrl+S**: Show window + audio on
- Useful for covert monitoring

**Technical:**
```python
# Hide
if keyboard.is_pressed('ctrl+h'):
    if not hidden:
        cv2.destroyWindow(win_name)
        hidden = True
        voice_active = False

# Show
if keyboard.is_pressed('ctrl+s'):
    if hidden:
        create_window()
        hidden = False
        voice_active = True
```

**Use cases:**
- Surprise monitoring
- Covert operations
- Aesthetics (hide on presentation, show on demand)

---

## 🎯 Motion Detection

**Feature:**
- Real-time motion/object detection
- Uses MOG2 (Mixture of Gaussians) algorithm
- Detects moving objects in scene

**Technical:**
```python
back_sub = cv2.createBackgroundSubtractorMOG2(
    history=500,           # How many frames to analyze
    varThreshold=50,       # Sensitivity (lower = more detections)
    detectShadows=True     # Ignore shadows
)

# In loop:
fg_mask = back_sub.apply(frame_hd)  # Creates mask of moving objects
```

**Why MOG2:**
- Robust against lighting changes
- Can detect shadows separately
- Better than simple frame difference

**Note:** Currently detects motion but doesn't trigger alerts. Can be extended for notifications.

---

## 🎛️ Camera Control

**What it does:**
- Adjusts camera brightness, contrast, exposure
- Optimizes image quality

**Technical:**
```python
cap.set(cv2.CAP_PROP_BRIGHTNESS, 230)   # 0-255
cap.set(cv2.CAP_PROP_CONTRAST, 130)     # Saturation
cap.set(cv2.CAP_PROP_EXPOSURE, -4)      # Light sensitivity
```

**Customization:**
Experiment with these values for your camera:
- Brightness: 0-255 (higher = brighter)
- Contrast: 50-200 (higher = more colors pop)
- Exposure: -13 to 0 (more negative = darker)

---

## 📊 Image Processing Pipeline

**Order of operations in each frame:**

1. **Read frame** from camera
2. **Resize** to camera_width × camera_height
3. **Gaussian blur** (3×3) - smoothing
4. **Sharpening** - edge enhancement
5. **Motion detection** - background subtraction
6. **Apply selected mode** (Normal/Clarity/Noise/Night/NVG)
7. **Add timestamp** - current date/time
8. **Display** - show on screen

This pipeline ensures consistent, high-quality output.

---

## ⚙️ Performance Optimization

**Frame rate factors:**
- Display mode (Clarity = slowest)
- Camera resolution (1080p > 720p)
- Computer CPU power
- Number of other applications running

**To improve FPS:**
1. Use Normal or Noise mode
2. Close background applications
3. Lower resolution if needed
4. Update graphics drivers

---

**Author:** GumbiTech  
**GitHub:** [@GumbiTech](https://github.com/GumbiTech)

**All features work together seamlessly for professional security monitoring!** 🎥
