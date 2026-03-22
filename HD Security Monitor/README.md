# HD Security Monitor - USB Webcam Application

A professional-grade Python security monitoring application with multiple vision filters, dynamic USB webcam support, and advanced image processing features.

## 🎯 Features

- **Multi-USB Webcam Support** - Automatically detects and lets you select from all connected USB cameras
- **Dynamic Resolution Control** - Automatically uses 1080p (1920×1080), falls back to 720p (1280×720) minimum
- **5 Display Modes**:
  - **Normal** - Standard camera feed with image enhancement
  - **Clarity** - Bilateral filtering for smooth, artifact-free images
  - **Noise** - Median blur for noise reduction
  - **Night** - CLAHE contrast enhancement for low-light conditions
  - **NVG** - Military night vision goggles effect (green monochrome with high contrast)

- **Advanced Features**:
  - Real-time motion detection using MOG2 background subtraction
  - Live timestamp display on video feed
  - Voice notifications ("system is now online")
  - Brightness/contrast/exposure adjustment
  - Sharpening and Gaussian blur filters
  - Ninja mode (hide window + mute audio)

## 📋 Requirements

### Hardware
- Windows PC (Python 3.7+)
- USB Webcam(s)

### Software
- Python 3.7 or higher
- Visual C++ Build Tools (for some packages)

## 🚀 Quick Start

### 1. Clone or Download Repository
```bash
git clone https://github.com/yourusername/hd-security-monitor.git
cd hd-security-monitor
```

### 2. Install Python Dependencies
```bash
pip install -r requirements.txt
```

### 3. Run the Application
```bash
python hd_cam.py
```

The script will:
1. Detect all connected USB cameras
2. Display a list with indices
3. Ask you to select a camera by entering its index
4. Initialize the camera with optimal settings
5. Display the live feed

## 🎮 Keyboard Controls

| Key | Function |
|-----|----------|
| **1** | Normal mode (standard feed) |
| **2** | Clarity mode (bilateral filter) |
| **3** | Noise reduction mode |
| **4** | Night mode (low-light enhancement) |
| **5** | NVG mode (military green style) |
| **Ctrl+H** | Hide window (Ninja mode ON) |
| **Ctrl+S** | Show window (Ninja mode OFF) |
| **Q** | Quit application |

## 📦 Dependencies

- **opencv-python** - Video capture and image processing
- **numpy** - Array operations
- **pyttsx3** - Text-to-speech notifications
- **keyboard** - Global keyboard input detection
- **plyer** - Desktop notifications
- **pygrabber** - Windows-specific camera detection (DirectShow)

All dependencies are listed in `requirements.txt`

## 🔧 Installation Guide

### Step 1: Setup Python Environment

**Option A: Using Virtual Environment (Recommended)**
```bash
# Create virtual environment
python -m venv venv

# Activate it
venv\Scripts\activate

# Install requirements
pip install -r requirements.txt
```

**Option B: Direct Installation**
```bash
pip install opencv-python numpy pyttsx3 keyboard plyer pygrabber
```

### Step 2: Run the Program
```bash
python hd_cam.py
```

## 🎨 Display Modes Explained

### Normal Mode
Standard camera feed with:
- Bilinear interpolation for clean scaling
- Light Gaussian blur smoothing
- Gentle sharpening kernel

### Clarity Mode
Enhanced clean image with:
- Bilateral filtering (9px neighborhood)
- Preserves edges while smoothing
- High sigma values (75, 75)

### Noise Reduction
Specialized noise removal:
- Median blur (5x5 kernel)
- Effective for salt-and-pepper noise
- Best for noisy camera feeds

### Night Mode
Low-light enhancement:
- CLAHE (Contrast Limited Adaptive Histogram Equalization)
- Enhances dark areas without overexposing bright areas
- Ideal for security monitoring in low-light

### NVG Mode (Night Vision Goggles)
Military-style green monitoring:
- Green monochrome color scheme
- High contrast CLAHE processing
- Gaussian blur and glow effect
- Vignette (darkened edges) for authenticity

## 🔍 Camera Resolution

The application intelligently handles resolution:
- **Attempts 1080p** (1920×1080) first for maximum quality
- **Falls back to 720p** (1280×720) if 1080p isn't supported
- **Never goes below 720p** for stable performance
- Status messages show which resolution was set

## 🎤 Voice Notifications

The application uses text-to-speech to announce:
- "system is now online" - When monitoring starts
- Supports customization (see code comments)

## 🕵️ Motion Detection

- Uses MOG2 (Mixture of Gaussians) background subtraction
- Detects moving objects in real-time
- Configurable history (500 frames) and variance threshold
- Shadow detection enabled for outdoor use

## 📝 Customization

### Adjust Camera Settings
Modify these values in the code:
```python
cap.set(cv2.CAP_PROP_BRIGHTNESS, 230)   # 0-255 range
cap.set(cv2.CAP_PROP_CONTRAST, 130)     # Adjust color saturation
cap.set(cv2.CAP_PROP_EXPOSURE, -4)      # Light sensor settings
```

### Change Window Size
```python
cv2.resizeWindow(win_name, 480, 270)  # Width, Height in pixels
```

### NVG Effect Customization
Modify these in `apply_nvg_effect()`:
```python
clahe = cv2.createCLAHE(clipLimit=3.0, tileGridSize=(8, 8))  # High contrast
blurred = cv2.GaussianBlur(enhanced, (5, 5), 0)  # Glow blur
```

## ⚠️ Troubleshooting

### "pip is not recognized"
Use: `python -m pip install -r requirements.txt`

### Camera not detected
- Check USB camera is connected
- Try re-plugging the camera
- Verify no other app is using the camera
- Check Device Manager (Windows) to see if camera is detected

### Low frame rate
- Switch to 720p if available (app auto-detects)
- Disable Clarity mode (most CPU intensive)
- Close other applications

### No audio
- Ensure pyttsx3 has valid TTS engines installed
- Check Windows Sound settings
- Run as Administrator if needed

## 📄 License

This project is provided as-is for personal and educational use.

## 📧 Support

For issues or questions, check the code comments or adjust settings as needed for your hardware.

---

**Developed by GumbiTech (Ntando Gumbi)**

Made with ❤️ for security monitoring  
GitHub: [@GumbiTech](https://github.com/GumbiTech)
