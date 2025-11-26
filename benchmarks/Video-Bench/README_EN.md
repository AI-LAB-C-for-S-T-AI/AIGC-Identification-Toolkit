# Video-Bench: Video Watermarking Robustness Evaluation Benchmark

[![Python 3.8+](https://img.shields.io/badge/python-3.8+-blue.svg)](https://www.python.org/downloads/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Dataset: VideoMarkBench](https://img.shields.io/badge/Dataset-VideoMarkBench-green.svg)](https://www.kaggle.com/datasets/zhengyuanjiang/videomarkbench/data)

> Evaluate the robustness of video watermarking algorithms (VideoSeal) under various video attacks, based on VideoMarkBench dataset.

---

## 🚀 Quick Start

### 1. Install Dependencies

```bash
# Core dependencies
pip install torch torchvision torchaudio

# Evaluation and visualization dependencies
pip install pytorch-msssim lpips scipy pyyaml tqdm matplotlib numpy
```

### 2. Download Dataset

Download VideoMarkBench dataset from Kaggle: [VideoMarkBench Dataset](https://www.kaggle.com/datasets/zhengyuanjiang/videomarkbench/data), then extract to `benchmarks/Video-Bench/dataset/VideoMarkBench/`



### 3. Run Evaluation

```bash
python benchmarks/Video-Bench/run_benchmark.py
```

**Results Output**: `benchmarks/Video-Bench/results/videoseal_robustness/`

---

## 📊 Evaluation Pipeline

### Supported Attack Types

| Attack Type | Strength Parameters | Description |
|---------|---------|------|
| **Gaussian Noise** | [0.01, 0.05, 0.10, 0.15, 0.20] | Frame-level: per-frame Gaussian noise (larger σ = stronger attack) |
| **Gaussian Blur** | [0.1, 0.5, 1.0, 1.5] | Frame-level: per-frame Gaussian blur (kernel standard deviation σ) |
| **JPEG Compression** | [90, 80, 60, 40, 20] | Frame-level: JPEG compression, lower quality = stronger distortion |
| **Crop & Resize** | [0.98, 0.96, 0.94, 0.92, 0.90] | Frame-level: crop then scale back to original size (retention ratio) |
| **Frame Average** | [1, 2, 3, 4, 5] | Video-level: sliding window frame averaging (larger window = stronger smoothing) |
| **Frame Swap** | [0.00, 0.05, 0.10, 0.15, 0.20] | Video-level: randomly swap adjacent frames (probability p) |
| **Frame Remove** | [0.00, 0.05, 0.10, 0.15, 0.20] | Video-level: randomly remove frames (probability p) |

Total: **7 attacks × multiple strength levels**, covering both spatial and temporal distortions.

### Evaluation Metrics

| Metric Category | Metric | Threshold | Description |
|----------|------|----------|----------|
| **Quality** | PSNR | ≥ 35.0 dB | Peak Signal-to-Noise Ratio, higher is better |
| **Quality** | SSIM | ≥ 0.95 | Structural Similarity Index, closer to 1 is better |
| **Quality** | tLP | ≤ 0.20 | Temporal LPIPS, measures cross-frame perceptual consistency, lower is better |
| **Robustness** | FNR | ≤ 0.01 | False Negative Rate (miss detection rate), lower indicates stronger robustness |
| **Robustness** | Bit Accuracy | ≥ 0.85 | Decoded bit accuracy, higher is better |

---

## 📈 Visualization Analysis

Generate radar charts and quality panels for quick comparison:

```bash
python benchmarks/Video-Bench/utils/plot_radar.py \
  benchmarks/Video-Bench/results/videoseal_robustness/metrics.json
```

<table>
  <tr>
    <th>FNR</th>
    <th>Bit Accuracy</th>
    <th>Quality Metrics</th>
  </tr>
  <tr>
    <td><img src="results/videoseal_robustness/videoseal_fnr_radar.png" alt="VideoSeal FNR Radar" /></td>
    <td><img src="results/videoseal_robustness/videoseal_bit_accuracy_radar.png" alt="VideoSeal Bit Accuracy Radar" /></td>
    <td style="vertical-align: top; height: 100%;">
      <table>
        <tr><th>Metric</th><th>Value</th><th style="white-space: nowrap;">Meets Threshold</th></tr>
        <tr><td><strong>PSNR</strong></td><td>40.59</td><td>✅</td></tr>
        <tr><td><strong>SSIM</strong></td><td>0.97</td><td>✅</td></tr>
        <tr><td><strong>tLP</strong></td><td>0.001</td><td>✅</td></tr>
      </table>
    </td>
  </tr>
</table>

> Each radar chart displays **5 curves** corresponding to 5 attack strength levels (from weak to strong).

---


## 🏆 Acknowledgements

This project is based on the following open source works:

- **[VideoMarkBench](https://www.kaggle.com/datasets/zhengyuanjiang/videomarkbench/data)** - Video attack implementation and evaluation framework
- **[VideoSeal](https://github.com/facebookresearch/videosse)** - Meta Research's video watermarking algorithm


---
