# Complete Setup Guide - HD Security Monitor

## Prerequisites

You need:
- **Windows 10/11** (or compatible Windows OS)
- **Python 3.7+** installed (download from python.org)
- **USB Webcam(s)** connected to your computer
- **Internet connection** (for downloading packages)

---

## Step-by-Step Setup

### Step 1: Install Python (if not already installed)

1. Go to [python.org](https://www.python.org/downloads/)
2. Download Python 3.10+ (or latest version)
3. Run the installer
4. **IMPORTANT**: Check "Add Python to PATH" during installation
5. Click Install

**Verify Installation:**
```bash
python --version
```

### Step 2: Clone or Download This Repository

**Option A: Using Git (if you have it installed)**
```bash
git clone https://github.com/yourusername/hd-security-monitor.git
cd hd-security-monitor
```

**Option B: Manual Download**
1. Click the green "Code" button on GitHub
2. Click "Download ZIP"
3. Extract the ZIP file to a folder
4. Open PowerShell/Command Prompt in that folder

### Step 3: Create Virtual Environment (Recommended)

This isolates project dependencies from your system Python.

**On Windows Command Prompt:**
```bash
python -m venv venv
venv\Scripts\activate
```

**On Windows PowerShell:**
```bash
python -m venv venv
.\venv\Scripts\Activate.ps1
```

You should see `(venv)` at the start of your terminal line.

### Step 4: Install Required Packages

```bash
pip install -r requirements.txt
```

Or manually install each package:
```bash
pip install opencv-python
pip install numpy
pip install pyttsx3
pip install keyboard
pip install plyer
pip install pygrabber
```

**Wait for all installations to complete. You should see "Successfully installed" messages.**

### Step 5: Verify Webcam Connection

Make sure your USB webcam(s) are plugged in:
1. Check Device Manager (Windows key → "Device Manager")
2. Look under "Cameras"
3. Your camera should be listed

### Step 6: Run the Application

```bash
python hd_cam.py
```

**Expected Output:**
```
--- Available Cameras ---
[0] : Integrated Camera
[1] : USB Webcam
[2] : External USB Camera
enter the index of the camera to use: 1
✓ Camera resolution set to 1080p (1920x1080)
system is now online.
Controls:
1=Normal | 2=Clarity | 3=Noise | 4=Night | 5=NVG
Ctrl+H = Hide | Ctrl+S = Show | Q = Quit
```

---

## Troubleshooting

### "Python is not recognized as an internal or external command"
**Solution:** 
- Reinstall Python and **CHECK "Add Python to PATH"**
- Or use: `py -m pip install` instead of `pip install`

### "No module named 'cv2' (or other packages)"
**Solution:**
```bash
pip install --upgrade pip
pip install -r requirements.txt
```

### "ModuleNotFoundError: No module named 'keyboard'"
```bash
pip install keyboard --user
```

### Camera Not Showing in List
**Solution:**
1. Unplug USB camera
2. Wait 5 seconds
3. Plug back in
4. Restart the script

### "Permission denied" error
**Solution:** Run PowerShell as Administrator
- Right-click PowerShell
- Select "Run as Administrator"

### Low FPS or Slow Performance
**Solutions:**
1. Close other applications (browsers, Chrome uses lots of CPU)
2. Switch to Noise or Normal mode (Clarity is most intensive)
3. Reduce window size (modify code)
4. Check if camera supports 720p (app will auto-detect)

### No Sound Output
**Solution:**
1. Check Windows Sound Settings
2. Verify speakers are not muted
3. Try setting: Settings → Sound → Volume up
4. Run as Administrator

---

## Testing the Installation

After running `python hd_cam.py`:

1. **Select a camera** (usually 0 or 1)
2. **Press number 5** to test NVG mode
3. **Press 1** to return to normal
4. **Press Q** to quit

If you see live video feed, congratulations! 🎉

---

## Updating the Application

To update to the latest version:

```bash
git pull origin main
pip install --upgrade -r requirements.txt
```

---

## Running Without Terminal Window

Create a `.bat` file to run the script without showing terminal:

1. Create a new file called `run.bat` in the same folder as `hd_cam.py`
2. Add this content:
```batch
@echo off
python hd_cam.py
pause
```
3. Double-click `run.bat` to launch

---

## Advanced: Customize on Startup

You can modify `hd_cam.py` to automatically select a camera (no selection prompt):

```python
# Replace this:
selected_camera, camera_name = get_camera_by_name()

# With this (camera 1):
selected_camera = 1
camera_name = "USB Webcam"
```

---

## Next Steps

- Read [FEATURES.md](FEATURES.md) for detailed feature explanations
- Read [CONTROLS.md](CONTROLS.md) for complete keyboard controls
- Check code comments for customization options

---

**Developed by GumbiTech (Ntando Gumbi)**

**Happy monitoring!** 🎥
