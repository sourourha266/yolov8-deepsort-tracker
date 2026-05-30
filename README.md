# 🎯 Real-Time Object Detection & Tracking — YOLOv8 + DeepSORT

> A real-time multi-object tracking pipeline combining **YOLOv8** spatial detection with **DeepSORT** temporal persistence — bridging the gap between frame-level recognition and identity-consistent tracking across video.

![Python](https://img.shields.io/badge/Python-3.10-blue?logo=python)
![YOLOv8](https://img.shields.io/badge/Detection-YOLOv8-red)
![DeepSORT](https://img.shields.io/badge/Tracking-DeepSORT-purple)
![OpenCV](https://img.shields.io/badge/Video-OpenCV-green)
![Colab](https://img.shields.io/badge/Runtime-Google%20Colab%20T4-orange?logo=googlecolab)

---

## 📌 Project Overview

Standard detection models classify objects per frame — with zero memory between them. This project adds **temporal persistence**: every detected object receives a stable ID that follows it across frames, even through occlusion and re-emergence.

| Component | Technology |
|-----------|-----------|
| **Detection** | YOLOv8n (Nano — optimized for real-time FPS) |
| **Tracking** | DeepSORT (Kalman Filter + Hungarian Algorithm + MobileNet embeddings) |
| **Video I/O** | OpenCV (`cv2`) |
| **Runtime** | Google Colab (T4 GPU recommended) |
| **Output** | Annotated MP4 with per-track bounding boxes, IDs, class labels, FPS HUD |

---

## ⚙️ How It Works

### Detection — YOLOv8 (The Eyes)
YOLOv8 processes the entire frame in a **single forward pass**, dividing it into a grid where each cell is responsible for objects whose center falls within it. The Nano variant (`yolov8n.pt`) is used to maximize FPS while preserving strong detection accuracy.

```
Frame → Grid Division → Single Forward Pass → [x, y, w, h, confidence, class]
```

### Tracking — DeepSORT (The Memory)

| Pillar | Role |
|--------|------|
| **Kalman Filter** | Predicts the next position of each track between frames |
| **Hungarian Algorithm** | Optimally matches new detections to existing tracks |
| **MobileNet Embeddings** | Visual fingerprinting — prevents ID switches when objects overlap |

DeepSORT maintains a stateful memory (`max_age=30` frames) so tracks survive brief occlusions before being dropped.

### Pipeline Flow

```
Video Frame
    │
    ▼
YOLOv8 Detection  ──►  [bbox, conf, class] per object
    │
    ▼
DeepSORT Update   ──►  Matched tracks with stable IDs
    │
    ▼
Draw Annotations  ──►  Colored boxes, #ID labels, class name, FPS counter
    │
    ▼
Write Output Frame
```

---

## 🚀 Quick Start

### Option 1 — Google Colab (Recommended)

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/sourourha266/yolov8-deepsort-tracker/blob/main/YOLOv8_DeepSORT.ipynb)

1. Open the notebook in Colab
2. Go to `Runtime → Change runtime type → T4 GPU`
3. Run all cells top-to-bottom
4. Upload your video when prompted in **Cell 3**
5. The annotated output downloads automatically

### Option 2 — Local

```bash
git clone https://github.com/sourourha266/yolov8-deepsort-tracker.git
cd yolov8-deepsort-tracker
pip install -r requirements.txt
# Then open the notebook in Jupyter
```

---

## 📦 Requirements

```
ultralytics>=8.0
deep-sort-realtime>=1.3
opencv-python-headless>=4.7
numpy>=1.23
```

Install:
```bash
pip install ultralytics deep-sort-realtime opencv-python-headless
```

---

## ⚡ Performance

Tested on Google Colab T4 GPU:

| Resolution | Model | Avg FPS |
|------------|-------|---------|
| 672 × 480 | YOLOv8n | **43.4 FPS** |

Real-time threshold is 24 FPS — this pipeline clears it comfortably.

---

## 🎨 Output Features

- **Unique color per track ID** — dynamically assigned via modulo-indexed RGB palette
- **Bounding box + label** — `#ID classname` rendered above each box
- **Live FPS counter** — top-right HUD overlay
- **Per-class object counter** — active track counts displayed per frame
- **H.264 re-encoding** — browser-compatible output via `ffmpeg`

---

## ⚠️ Known Limitations

| Edge Case | Cause | Effect |
|-----------|-------|--------|
| **Severe Occlusion** | Track hidden > 30 frames (`max_age`) | ID switch on re-emergence |
| **Fast / Erratic Motion** | Kalman filter assumes linear velocity | Trajectory model breaks |
| **Illumination Shifts** | Drastic pixel value changes | Cosine distance matching fails |

---

## 🌍 Real-World Applications

- **Intelligent Surveillance** — class-filtered alerts for specific object intrusions
- **Autonomous Driving** — pedestrian trajectory and velocity estimation for collision avoidance
- **Sports Analytics** — persistent player IDs for velocity stats and spatial heatmaps

---

## 🗂️ Repository Structure

```
yolov8-deepsort-tracker/
│
├── YOLOv8_DeepSORT.ipynb      # Main Colab notebook (10 cells)
├── requirements.txt
├── .gitignore
└── README.md
```

---

## 📓 Notebook Structure

| Cell | Description |
|------|-------------|
| 1 | Install dependencies |
| 2 | Imports |
| 3 | Upload input video |
| 4 | Configuration (model, confidence, output path) |
| 5 | Helper functions (FPS overlay, counter HUD) |
| 6 | Load YOLOv8 + DeepSORT models |
| 7 | Main tracking loop 🚀 |
| 8 | Re-encode to H.264 for browser playback |
| 9 | Preview output in notebook |
| 10 | Download annotated video |

---

## 👩‍💻 Author

**Sourour Hammoud**  
M.Eng. Candidate — Computer & Communication Engineering / IoT & Smart Systems  
Supervised by: Pr. Mohamad KHALIL  
[Portfolio](https://sourourhammoud.netlify.app)

---

## 📄 License

This project is licensed under the MIT License.
