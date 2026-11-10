# Exercise 16: CamShift Sample Code Explanation

**Source:**  
[https://github.com/opencv/opencv/blob/3.4/samples/python/camshift.py](https://github.com/opencv/opencv/blob/3.4/samples/python/camshift.py)

This file is an interactive demo of the **CAMSHIFT** (Continuously Adaptive Mean Shift) tracking algorithm.  
The user selects a colored object (e.g., a face) with the mouse, and the program tracks it in a video stream or webcam feed.

---

## 1. Introduction & Usage Guide (Docstring)

The docstring at the top of the file explains:

- This demo is based on Mean-Shift / CamShift tracking.
- The video source can be a webcam (default index `0`) or a video file path.
- To start tracking, select the object with the mouse.
- **ESC** → Exit the program  
- **b** → Toggle back-projected probability visualization

---

## 2. Python 2 / 3 Compatibility

```python
from __future__ import print_function
import sys
PY3 = sys.version_info[0] == 3
if PY3:
    xrange = range
```

This block ensures compatibility with older Python 2 code.  
In modern Python 3 it is not strictly necessary.

---

## 3. Imports

```python
import numpy as np
import cv2 as cv
import video
from video import presets
```

| Module | Purpose |
|--------|---------|
| `numpy` | Array operations |
| `cv2` | OpenCV image/video processing |
| `video` | Local OpenCV helper module for easier camera/video capture |

---

## 4. Class `App` — Constructor (`__init__`)

```python
class App(object):
    def __init__(self, video_src):
        self.cam = video.create_capture(video_src, presets['cube'])
        _ret, self.frame = self.cam.read()
        cv.namedWindow('camshift')
        cv.setMouseCallback('camshift', self.onmouse)

        self.selection = None
        self.drag_start = None
        self.show_backproj = False
        self.track_window = None
```

**What it does:**

- Opens the video source (webcam or file).
- Creates a display window and registers a mouse callback.
- Initializes tracking state variables:

| Variable | Meaning |
|----------|---------|
| `selection` | Coordinates of the region currently being selected |
| `drag_start` | Starting point of the mouse drag |
| `show_backproj` | Whether to show the back-projection map |
| `track_window` | Search window for CamShift `(x, y, w, h)` |

---

## 5. Mouse Callback — `onmouse`

```python
def onmouse(self, event, x, y, flags, param):
    if event == cv.EVENT_LBUTTONDOWN:
        self.drag_start = (x, y)
        self.track_window = None
    if self.drag_start:
        xmin = min(x, self.drag_start[0])
        ymin = min(y, self.drag_start[1])
        xmax = max(x, self.drag_start[0])
        ymax = max(y, self.drag_start[1])
        self.selection = (xmin, ymin, xmax, ymax)
    if event == cv.EVENT_LBUTTONUP:
        self.drag_start = None
        self.track_window = (xmin, ymin, xmax - xmin, ymax - ymin)
```

**Step-by-step:**

1. **Left button down** → Save starting point and cancel previous tracking.
2. **Mouse drag** → Continuously update the selection rectangle.
3. **Left button up** → Finalize the ROI as `(x, y, width, height)`.  
   This becomes the initial search window for CamShift.

---

## 6. Histogram Visualization — `show_hist`

```python
def show_hist(self):
    bin_count = self.hist.shape[0]
    bin_w = 24
    img = np.zeros((256, bin_count * bin_w, 3), np.uint8)
    for i in xrange(bin_count):
        h = int(self.hist[i])
        cv.rectangle(img, (i * bin_w + 2, 255),
                     ((i + 1) * bin_w - 2, 255 - h),
                     (int(180.0 * i / bin_count), 255, 255), -1)
    img = cv.cvtColor(img, cv.COLOR_HSV2BGR)
    cv.imshow('hist', img)
```

- Draws the Hue histogram of the selected region.
- Each bar represents one Hue bin; height shows frequency.
- Helps the user understand the color model learned from the ROI.

---

## 7. Main Loop — `run` (Core of the Algorithm)

### 7.1 Frame Preparation

```python
_ret, self.frame = self.cam.read()
vis = self.frame.copy()
hsv = cv.cvtColor(self.frame, cv.COLOR_BGR2HSV)
mask = cv.inRange(hsv, np.array((0., 60., 32.)),
                       np.array((180., 255., 255.)))
```

- Read the next frame.
- Convert to **HSV** color space (Hue is more robust for color tracking).
- Create a mask to ignore very dark or low-saturation pixels  
  (`S ∈ [60, 255]`, `V ∈ [32, 255]`).

---

### 7.2 Building the Color Model (While Selecting)

```python
if self.selection:
    x0, y0, x1, y1 = self.selection
    hsv_roi = hsv[y0:y1, x0:x1]
    mask_roi = mask[y0:y1, x0:x1]
    hist = cv.calcHist([hsv_roi], [0], mask_roi, [16], [0, 180])
    cv.normalize(hist, hist, 0, 255, cv.NORM_MINMAX)
    self.hist = hist.reshape(-1)
    self.show_hist()

    vis_roi = vis[y0:y1, x0:x1]
    cv.bitwise_not(vis_roi, vis_roi)
    vis[mask == 0] = 0
```

- Extract the ROI from the HSV image and mask.
- Compute a **16-bin Hue histogram**.
- Normalize the histogram to the range `[0, 255]`.
- Temporarily invert the selected region for visual feedback.
- Black out pixels outside the mask.

---

### 7.3 Tracking with CamShift

```python
if self.track_window and self.track_window[2] > 0 and self.track_window[3] > 0:
    self.selection = None
    prob = cv.calcBackProject([hsv], [0], self.hist, [0, 180], 1)
    prob &= mask
    term_crit = (cv.TERM_CRITERIA_EPS | cv.TERM_CRITERIA_COUNT, 10, 1)
    track_box, self.track_window = cv.CamShift(prob, self.track_window, term_crit)

    if self.show_backproj:
        vis[:] = prob[..., np.newaxis]
    try:
        cv.ellipse(vis, track_box, (0, 0, 255), 2)
    except:
        print(track_box)
```

| Step | Function | Description |
|------|----------|-------------|
| 1 | `calcBackProject` | Creates a probability map — how similar each pixel is to the object histogram |
| 2 | `prob &= mask` | Removes invalid / noisy regions |
| 3 | `term_crit` | Stop after 10 iterations **or** when movement < 1 pixel |
| 4 | `cv.CamShift` | Finds the new center (MeanShift) and adapts **size + orientation** |
| 5 | `cv.ellipse` | Draws a red ellipse around the tracked object |

**Key difference from MeanShift:**  
CamShift also updates the window size and rotation, making it more robust to scale and orientation changes.

---

### 7.4 Display & Keyboard Control

```python
cv.imshow('camshift', vis)
ch = cv.waitKey(5)
if ch == 27:          # ESC
    break
if ch == ord('b'):    # toggle back-projection
    self.show_backproj = not self.show_backproj
cv.destroyAllWindows()
```

---

## 8. Program Entry Point

```python
if __name__ == '__main__':
    print(__doc__)
    import sys
    try:
        video_src = sys.argv[1]
    except:
        video_src = 0
    App(video_src).run()
```

- If a command-line argument is given → use it as the video source.  
- Otherwise → open the default webcam (`0`).  
- Create an `App` instance and start the main loop.

---

## 9. Overall Algorithm Flow

1. User selects a colored region with the mouse.  
2. A Hue histogram of that region is computed (object model).  
3. For every new frame:  
   - Compute the back-projection of the histogram.  
   - Run CamShift to update location, size, and orientation.  
4. Draw a red ellipse on the tracked object.  
5. Repeat until the user presses **ESC**.

---

## 10. Educational Notes

- **CamShift** is an improved version of MeanShift: it adapts both scale and orientation.
- Tracking quality depends heavily on a good initial ROI and sufficient color contrast with the background.
- The HSV mask helps reject shadows and low-light noise.
- Because the method is purely color-based, large appearance changes or similar-colored backgrounds can cause tracking failure.

---

*End of explanation*
