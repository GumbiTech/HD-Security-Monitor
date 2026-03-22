# Keyboard Controls Reference

## Quick Control Map

```
┌─────────────┬──────────────────────────────┐
│   KEY(s)    │      ACTION                   │
├─────────────┼──────────────────────────────┤
│      1      │ Normal Mode                  │
│      2      │ Clarity Mode                 │
│      3      │ Noise Reduction Mode         │
│      4      │ Night Mode                   │
│      5      │ NVG (Night Vision) Mode      │
├─────────────┼──────────────────────────────┤
│   Ctrl+H    │ Hide                         │
│   Ctrl+S    │ Show                         │
├─────────────┼──────────────────────────────┤
│      Q      │ Quit Application             │
└─────────────┴──────────────────────────────┘
```

---

## Display Mode Controls

### Press 1: Normal Mode
- **Effect:** Standard surveillance feed
- **Best for:** General 24/7 monitoring
- **Description:** Clean, enhanced standard camera feed with gentle sharpening
- **Response time:** Instant mode switching

### Press 2: Clarity Mode
- **Effect:** High-quality detailed view
- **Best for:** Well-lit environments requiring detail
- **Description:** Bilateral filtering removes noise while keeping edges sharp
- **Note:** Slightly slower processing (higher CPU usage)

### Press 3: Noise Reduction Mode
- **Effect:** Smooth, blur-filtered view
- **Best for:** Noisy camera sensors, low-quality cameras
- **Description:** Median blur removes grain and noise
- **Response time:** Fast, low CPU usage

### Press 4: Night Mode
- **Effect:** Enhanced visibility in darkness
- **Best for:** Low-light and night monitoring
- **Description:** CLAHE contrast enhancement brings out details in shadows
- **Note:** May cause overexposure in bright areas

### Press 5: NVG Mode (Night Vision Goggles)
- **Effect:** Military green monochrome tactical look
- **Best for:** Special effects, themed surveillance, tactical aesthetics
- **Description:** Green monochrome with high contrast and vignette
- **Technical:** Grayscale → green channel only → vignette effect
- **Unique features:** Glowing highlights, darkened corners, very high contrast

---

## View Control (Ninja Mode)

### Press Ctrl+H: Hide Window
- **Action:** Closes the video display window
- **Audio:** Mutes voice notifications
- **Status:** Prints "Ninja Mode: ON" to console
- **Background:** Application continues running
- **Use case:** Covert monitoring without visible window

### Press Ctrl+S: Show Window  
- **Action:** Reopens the video display window
- **Audio:** Re-enables voice notifications
- **Status:** Prints "Ninja Mode: OFF" to console
- **Previous state:** Restores display settings
- **Use case:** Resume visible monitoring

**Ninja Mode Workflow:**
1. Start monitoring (visible)
2. Press Ctrl+H (window disappears, hidden monitoring)
3. Application runs in background
4. Press Ctrl+S (window reappears)

---

## Exit Application

### Press Q: Quit
- **Action:** Cleanly exits the application
- **Cleanup:** Releases camera resources
- **Window:** Closes all OpenCV windows
- **Response:** Application terminates immediately

**What happens on exit:**
```python
if cv2.waitKey(1) & 0xFF == ord('q'):
    break  # Exit main loop

cap.release()              # Release camera
cv2.destroyAllWindows()    # Close windows
```

---

## Advanced Control Sequences

### Mode Switching Workflow
```
Start (Normal Mode)
    ↓
Press 5 → NVG Mode (green military look)
    ↓
Press 4 → Night Mode (bright enhancement)
    ↓
Press 1 → Back to Normal Mode
```

All mode switches are **instant** - no delay or lag.

### Hide/Show + Mode Switching
```
Press Ctrl+H (window hidden, monitoring continues)
    ↓
Press 1,2,3,4,5 (mode changes, but not visible)
    ↓
Press Ctrl+S (window shows with selected mode)
```

### Performance Optimization
If too slow:
```
Current Mode: 5 (NVG - most resource intensive)
    ↓
Press 1 (Normal - least intensive, faster FPS)
```

---

## Control Timing

### Response Time
- **Mode switching (1-5):** Instant (<1ms)
- **Hide/Show (Ctrl+H/Ctrl+S):** Instant (<1ms)
- **Quit (Q):** 1-2 frames delay
- **Timestamp update:** Every frame (<33ms at 30fps)

### Repeat Key Behavior
```
# Pressing same key multiple times:
Press 5, 5, 5, 5 → Stays in NVG mode (no flickering)
Press Ctrl+H, Ctrl+H, Ctrl+H → Stays hidden (not redundant)
```

---

## Troubleshooting Controls

### Mode won't switch
- **Check:** Make sure OpenCV window is focused/active
- **Try:** Try pressing the key again
- **Note:** Some keyboard drivers may not register rapid presses

### Hide (Ctrl+H) Not Working
- **Check:** Window must be visible first
- **Try:** Make sure the OpenCV window has focus
- **Alt:** Click the OpenCV window to make it active

### Show (Ctrl+S) But Window Doesn't Appear
- **Check:** Window might be off-screen
- **Try:** Press Ctrl+S again, it should reappear
- **Note:** Window is set to always-on-top, so should appear above everything

### Quit (Q) Doesn't Work
- **Check:** OpenCV window must be active
- **Try:** Click on the video window first, then press Q
- **Alt:** Close the window manually (X button)

---

## Keyboard Layout Reference

### Number Keys (Mode Selection)
```
┌─────┬─────┬─────┬─────┬─────┐
│  1  │  2  │  3  │  4  │  5  │
│ NOR │CLR  │ NOI │ NIT │ NVG │
└─────┴─────┴─────┴─────┴─────┘
```

### Modifier Keys (View Control)
```
              ┌──────────────┐
              │   Ctrl+H     │  Hide
              │     Hide     │
              ├──────────────┤
              │   Ctrl+S     │  Show
              │     Show     │
              └──────────────┘
```

### Exit Key
```
┌─────────────────────────────┐
│            Q - Quit         │
└─────────────────────────────┘
```

---

## Quick Reference Cheatsheet

**For Fast Switching Between Modes:**

| Task | Keys |
|------|------|
| Brighten scene | Press 5 (NVG) |
| Remove noise | Press 3 (Noise) |
| Get details | Press 2 (Clarity) |
| Night surveillance | Press 4 (Night) |
| Standard view | Press 1 (Normal) |
| Hide monitoring | Press Ctrl+H |
| Resume monitoring | Press Ctrl+S |
| Exit program | Press Q |

---

## Example Use Cases

### Scenario 1: Day to Night Transition
```
12:00 PM - Press 1 (Normal mode, good daytime feed)
    ↓
06:00 PM - Press 4 (Night mode, getting darker)
    ↓
09:00 PM - Press 5 (NVG mode, full night vision effect)
```

### Scenario 2: Covert Monitoring Session
```
Press 1 → Normal mode visible
    ↓
Press Ctrl+H → Window hides, monitoring continues
    ↓
[Monitoring happens invisibly for 10 minutes]
    ↓
Press Ctrl+S → Window reappears with footage
```

### Scenario 3: Noisy Camera Fix
```
Press 1 → Normal mode (grainy feed)
    ↓
Press 3 → Noise reduction applied (grain removed)
    ↓
Better image quality for rest of session
```

---

## Accessibility Notes

- **All controls are single-key or simple combos** (no complex sequences required)
- **No mouse required** (keyboard only)
- **Feedback:** Console prints status ("Ninja Mode: ON/OFF")
- **Visual feedback:** Mode displays on video (top-left corner)

---

## Pro Tips

1. **Fastest mode for 24/7 monitoring:** Normal (1) or Noise (3)
2. **Best quality for detail work:** Clarity (2)
3. **Best for night:** Night (4) or NVG (5)
4. **For presentations:** Use Ninja mode (Ctrl+H before showing, Ctrl+S to resume)
5. **If lagging:** Switch to Normal (1) or Noise (3) mode

---

**Developed by GumbiTech**

**Master these controls for professional security monitoring!** 🎥
