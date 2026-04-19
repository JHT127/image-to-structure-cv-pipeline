# Image-to-Structure: A Computer Vision Pipeline

A clean, end-to-end image processing pipeline that transforms a color photograph into a structured geometric representation using classical computer vision techniques.

> Built as part of a Computer Vision course. Presented here as a standalone project because the concepts — and the decisions behind them — are worth documenting properly.

---

## What This Project Does

Most images contain far more information than a task needs. This pipeline progressively strips an image down to its essential structure — keeping what matters, discarding what doesn't — through a series of deliberate transformations:

```
Color Image  →  Grayscale  →  Noise Reduction  →  Edge Detection  →  Structural Representation
```

Each step is a conscious trade-off between information richness and computational simplicity. The notebook documents not just *what* each step does, but *why* it's done in that order, and what is gained and lost at each stage.

---

## Pipeline Overview

| Stage | Technique | Purpose |
|---|---|---|
| Load | OpenCV imread | Read image as a 3D NumPy array (H × W × 3) |
| Grayscale | Weighted channel collapse | Remove color, preserve luminance structure |
| Smoothing | Gaussian blur (3×3) | Suppress high-frequency noise before gradient computation |
| Edge detection | Sobel + Canny (compared) | Locate boundaries via intensity gradient analysis |
| Structure | Contour extraction | Convert edge pixels into geometric curves |

---

## Key Technical Decisions

**Why Gaussian blur before edge detection?**  
Edge detectors measure the gradient of pixel intensity. Random sensor noise creates sharp local gradients that are indistinguishable from real edges. Smoothing suppresses these before they can be mistakenly flagged.

**Why compare Sobel and Canny instead of just picking one?**  
They answer different questions. Sobel produces a continuous gradient magnitude map — useful when you care about *how strong* an edge is. Canny produces a binary, single-pixel-wide result — useful when you care about *where* an edge is. Understanding the difference matters more than picking a winner.

**Why use `CV_64F` in the Sobel computation?**  
The default `uint8` clips negative gradient values to zero, which means you lose half of the gradient information (edges only detected in one direction). 64-bit float preserves the full signed gradient, and we take the absolute value afterwards.

---

## Results

The pipeline produces five output images, saved automatically to `outputs/`:

| Output | Description |
|---|---|
| `step1_grayscale.jpg` | Single-channel luminance image |
| `step2_smoothed.jpg` | Noise-reduced grayscale |
| `step3a_sobel_edges.jpg` | Gradient magnitude map |
| `step3b_canny_edges.jpg` | Binary edge map (strict thresholds) |
| `step4_overlay.jpg` | Detected contours overlaid on original |
| `full_pipeline.jpg` | All five stages side by side |

---

## Project Structure

```
image-to-structure-cv-pipeline/
│
├── data/
│   └── img.jpg                              # Input image
│
├── outputs/                                 # Auto-created on first run
│   ├── step1_grayscale.jpg
│   ├── step2_smoothed.jpg
│   ├── step3a_sobel_edges.jpg
│   ├── step3b_canny_edges.jpg
│   ├── step4_edge_map.jpg
│   ├── step4_contours.jpg
│   ├── step4_overlay.jpg
│   └── full_pipeline.jpg
│
├── JoudThaher_assign1_image2structure.ipynb # Main notebook
├── requirements.txt                         # Dependencies
└── README.md
```

---

## Setup and Usage

**Requirements**

```bash
pip install -r requirements.txt
```

**Run**

```bash
jupyter notebook JoudThaher_assign1_image2structure.ipynb
```

Run all cells in order. The `outputs/` folder will be created automatically.

---

## Dependencies

```
opencv-python
numpy
matplotlib
jupyter
```

Or install directly:

```bash
pip install opencv-python numpy matplotlib jupyter
```

---

## Concepts Covered

- Digital image representation (3D arrays, pixel values, data types)
- Color channels and the BGR vs RGB distinction in OpenCV
- Histogram analysis for understanding pixel intensity distributions
- Grayscale conversion using perceptual luminance weights
- Gaussian filtering and frequency-domain intuition for noise
- Gradient-based edge detection (Sobel operator)
- Multi-stage edge detection with hysteresis (Canny algorithm)
- Contour extraction and geometric representation of boundaries

---

## What I Learned

The most interesting part of this project wasn't the code — it was understanding *why* the pipeline is ordered the way it is. Smoothing before edge detection isn't just a convention; it's mathematically necessary because the operations don't commute cleanly. And choosing between Sobel and Canny isn't a matter of one being better — it's a matter of what question you're asking the image.

Classical computer vision is full of these kinds of decisions. Libraries make them easy to implement, but understanding the reasoning behind them is what makes the difference between using a tool and actually knowing what you're doing.

---

## Author

**Joud Thaher**  
Computer Vision — 2026
