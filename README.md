## Hand Gesture Recognition for Cursor Control
Control your mouse with hand gestures detected from a webcam using MediaPipe Hands and PyAutoGUI.

Project structure:
- `app.py`: Captures webcam frames, runs MediaPipe Hands, draws landmarks, and delegates actions.
- `controller.py`: Maps hand landmark states to cursor movement, scrolling, zooming, clicks, and dragging.
- `requirements.txt`: Pinned dependencies known to work on Windows with Python 3.11.
- `gestures/`: Reference images for each gesture.

### Prerequisites
- Windows 10/11 with a working webcam
- Python 3.11 (installed via Microsoft Store or python.org)

### Quick Start (Windows, PowerShell)
From the project root:

```powershell
# 1) Use a Python 3.11 environment (recommended)
# Create a venv locally
py -3.11 -m venv .venv311
.\.venv311\Scripts\Activate.ps1

# 2) Install dependencies
python -m pip install --upgrade pip
python -m pip install -r requirements.txt

# 3) Run
python app.py
```

To stop: Press Esc in the OpenCV window, or press Ctrl+C in the terminal.

### Gestures and Actions
The following mappings reflect the actual logic in `controller.py`:

- Cursor moving: Default behavior. The cursor follows landmark 9 unless frozen.
- Cursor freezing: Thumb down while all other fingers up. Prevents mouse movement.
  - Reference: `gestures/Mouse_freezing.png`

- Drag and drop: All fingers down (fist). Holds left mouse button until you release the fist.
  - Reference: `gestures/Drag.png`

- Left click: Index finger tip inside the thumb area; middle, ring, little are up; no other finger inside thumb.
  - Reference: `gestures/Left_click.png`

- Right click: Middle finger tip inside the thumb area; index, ring, little are up; no other finger inside thumb.
  - Reference: `gestures/Right_click.png`

- Double click: Ring finger tip inside the thumb area; index, middle, little are up; no other finger inside thumb.
  - Reference: `gestures/Double_click.png`

- Scroll up: Little finger up; index, middle, ring down.
  - Reference: `gestures/Scrolling_up.png`

- Scroll down: Index finger up; middle, ring, little down.
  - Reference: `gestures/Scrolling_down.png`

- Zoom: Index and middle up; ring and little down. Zoom direction depends on horizontal spacing:
  - Zoom out: Index and middle are close together (within a small x-distance window).
    - Reference: `gestures/Zooming_out.png`
  - Zoom in: Index and middle are apart (outside that window).
    - Reference: `gestures/Zooming_in.png`

Notes:
- Landmark indices follow MediaPipe Hands (e.g., 4 = thumb tip, 8 = index tip, 12 = middle tip, 16 = ring tip, 20 = little tip).
- The controller clamps the cursor near screen edges and smooths deltas relative to the previous hand position.

### Troubleshooting
- Import errors (cv2 / mediapipe not found): Ensure you are using the project venv.
  - Activate: `.\.venv311\Scripts\Activate.ps1`
  - Verify: `python -c "import cv2, mediapipe; print(cv2.__version__, mediapipe.__version__)"`
- Camera not opening or black frame: Close other apps using the webcam. Try a different camera index in `app.py` (`cv2.VideoCapture(1)` etc.).
- Window not closing: Press Esc in the OpenCV window. If needed, press Ctrl+C in terminal. The app releases the camera and closes windows on exit.
- Sensitivity/steering: The movement uses landmark 9; adjust logic in `Controller.get_position` (`ratio`, `threshold`) to tune.

### How It Works
`app.py` flips the frame, converts to RGB, runs MediaPipe Hands, draws landmarks, and updates global hand state used by `controller.py`. The controller computes finger-up/down states, detects composite gestures, and uses PyAutoGUI to control the OS cursor and input.

### License
Apache-2.0. See `LICENSE` if present.
