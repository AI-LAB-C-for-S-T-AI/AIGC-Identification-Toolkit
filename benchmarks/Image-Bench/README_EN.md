# Image-Bench: Image Watermark Robustness Evaluation Benchmark

[![Python 3.8+](https://img.shields.io/badge/python-3.8+-blue.svg)](https://www.python.org/downloads/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Dataset: W-Bench](https://img.shields.io/badge/Dataset-W--Bench-green.svg)](https://huggingface.co/datasets/Shilin-LU/W-Bench)

> Evaluate the robustness of image watermarking algorithms under traditional distortion attacks, based on the W-Bench DISTORTION_1K dataset (1000 images × 25 attack configurations).


---

## 🚀 Quick Start

### 1. Install Dependencies

```bash
# Required dependencies
pip install pillow numpy torch tqdm pyyaml

# Quality metrics calculation
pip install scikit-image lpips
```

### 2. Download Dataset


```bash
huggingface-cli download Shilin-LU/W-Bench \
  --repo-type=dataset \
  --local-dir dataset/W-Bench \
  --include "DISTORTION_1K/**"
```

**Dataset Size**: ~9GB
**Verify Download**: `ls dataset/W-Bench/DISTORTION_1K/image/ | wc -l` should output 1000

### 3. Run Evaluation

```bash
python benchmarks/Image-Bench/run_benchmark.py
```

**Output Results**: `results/videoseal_distortion/metrics.json`

---

## 📊 Evaluation Pipeline

### Supported Attack Types

| Attack Type | Intensity Parameters | Description |
|---------|---------|------|
| **Brightness** | [1.2, 1.4, 1.6, 1.8, 2.0] | Brightness enhancement (multiplier) |
| **Contrast** | [0.2, 0.4, 0.6, 0.8, 1.0] | Contrast reduction (multiplier) |
| **Blurring** | [1, 3, 5, 7, 9] | Gaussian blur (kernel size) |
| **Noise** | [0.01, 0.03, 0.05, 0.07, 0.1] | Gaussian noise (standard deviation) |
| **JPEG Compression** | [95, 90, 80, 70, 60] | JPEG quality |

Total: **5 attacks × 5 intensities = 25 configurations**

### Evaluation Metrics


| Metric Category | Metric | Threshold | Description |
|----------|------|----------|----------|
| **Quality** | PSNR | ≥ 35.0 dB | Peak Signal-to-Noise Ratio, higher is better |
| **Quality** | SSIM | ≥ 0.95 | Structural Similarity Index, closer to 1 is better |
| **Quality** | LPIPS | ≤ 0.015 | Learned Perceptual Similarity, lower is better |
| **Robustness** | TPR | ≥ 0.80 | True Positive Rate (detection success rate), higher indicates stronger robustness |
| **Robustness** | Bit Accuracy | ≥ 0.85 | Watermark bit accuracy, determines closeness of decoded result to original watermark |

---

## 📈 Visualization Analysis

Generate radar charts to visualize watermark robustness:

```bash
python benchmarks/Image-Bench/utils/plot_radar.py \
    benchmarks/Image-Bench/results/videoseal_distortion/metrics.json
```
<table>
  <tr>
    <th>TPR</th>
    <th>Bit Accuracy</th>
    <th>Quality Metrics</th>
  </tr>
  <tr>
    <td><img src="results/videoseal_distortion/videoseal_tpr_radar.png" alt="VideoSeal TPR Radar" /></td>
    <td><img src="results/videoseal_distortion/videoseal_bit_accuracy_radar.png" alt="VideoSeal Bit Accuracy Radar" /></td>
    <td style="vertical-align: top; height: 100%;">
      <table>
        <tr><th>Metric</th><th>Value</th><th style="white-space: nowrap;">Meets Threshold</th></tr>
        <tr><td><strong>PSNR</strong></td><td>45.52 dB</td><td>✅</td></tr>
        <tr><td><strong>SSIM</strong></td><td>0.9953</td><td>✅</td></tr>
        <tr><td><strong>LPIPS</strong></td><td>0.0025</td><td>✅</td></tr>
      </table>
    </td>
  </tr>
</table>





---

## 🏆 Acknowledgements

This project is based on the following open-source works:

- **[VINE](https://github.com/Shilin-LU/VINE)** - W-Bench dataset and distortion attack implementations
- **[VideoSeal](https://github.com/facebookresearch/videoseal)** - Video/image watermarking algorithm




