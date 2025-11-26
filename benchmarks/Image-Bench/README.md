# Image-Bench: 图像水印鲁棒性评估基准

[![Python 3.8+](https://img.shields.io/badge/python-3.8+-blue.svg)](https://www.python.org/downloads/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Dataset: W-Bench](https://img.shields.io/badge/Dataset-W--Bench-green.svg)](https://huggingface.co/datasets/Shilin-LU/W-Bench)

> 评估图像水印算法在传统失真攻击下的鲁棒性，基于W-Bench DISTORTION_1K数据集（1000张图像 × 25种攻击配置）。


---

## 🚀 快速开始

### 1. 安装依赖

```bash
# 必需依赖
pip install pillow numpy torch tqdm pyyaml

# 质量指标计算
pip install scikit-image lpips
```

### 2. 下载数据集


```bash
huggingface-cli download Shilin-LU/W-Bench \
  --repo-type=dataset \
  --local-dir dataset/W-Bench \
  --include "DISTORTION_1K/**"
```

**数据集大小**: ~9GB
**验证下载**: `ls dataset/W-Bench/DISTORTION_1K/image/ | wc -l` 应输出 1000

### 3. 运行评估

```bash
python benchmarks/Image-Bench/run_benchmark.py
```

**结果输出**: `results/videoseal_distortion/metrics.json`

---

## 📊 评估流程

### 支持的攻击类型

| 攻击类型 | 强度参数 | 说明 |
|---------|---------|------|
| **Brightness** | [1.2, 1.4, 1.6, 1.8, 2.0] | 亮度增强（倍数） |
| **Contrast** | [0.2, 0.4, 0.6, 0.8, 1.0] | 对比度降低（倍数） |
| **Blurring** | [1, 3, 5, 7, 9] | 高斯模糊（核大小） |
| **Noise** | [0.01, 0.03, 0.05, 0.07, 0.1] | 高斯噪声（标准差） |
| **JPEG Compression** | [95, 90, 80, 70, 60] | JPEG质量 |

共计 **5种攻击 × 5个强度 = 25种配置**

### 评估指标


| 指标类别 | 指标 | 判定阈值 | 指标说明 |
|----------|------|----------|----------|
| **质量** | PSNR | ≥ 35.0 dB | Peak Signal-to-Noise Ratio（峰值信噪比），越高越好 |
| **质量** | SSIM | ≥ 0.95 | Structural Similarity Index（结构相似度），越接近 1 越好 |
| **质量** | LPIPS | ≤ 0.015 | Learned Perceptual Similarity（感知相似度），越低越好 |
| **鲁棒性** | TPR | ≥ 0.80 | True Positive Rate（检测成功率），越高表示鲁棒性越强 |
| **鲁棒性** | Bit Accuracy | ≥ 0.85 | 水印比特准确率，决定解码结果与原始水印的接近程度 |

---

## 📈 可视化分析

生成雷达图以可视化水印鲁棒性：

```bash
python benchmarks/Image-Bench/utils/plot_radar.py \
    benchmarks/Image-Bench/results/videoseal_distortion/metrics.json
```
<table>
  <tr>
    <th>TPR</th>
    <th>Bit Accuracy</th>
    <th>质量评估指标</th>
  </tr>
  <tr>
    <td><img src="results/videoseal_distortion/videoseal_tpr_radar.png" alt="VideoSeal TPR Radar" /></td>
    <td><img src="results/videoseal_distortion/videoseal_bit_accuracy_radar.png" alt="VideoSeal Bit Accuracy Radar" /></td>
    <td style="vertical-align: top; height: 100%;">
      <table>
        <tr><th>指标</th><th>数值</th><th style="white-space: nowrap;">达到阈值</th></tr>
        <tr><td><strong>PSNR</strong></td><td>45.52 dB</td><td>✅</td></tr>
        <tr><td><strong>SSIM</strong></td><td>0.9953</td><td>✅</td></tr>
        <tr><td><strong>LPIPS</strong></td><td>0.0025</td><td>✅</td></tr>
      </table>
    </td>
  </tr>
</table>





---

## 🏆 致谢

本项目基于以下开源工作：

- **[VINE](https://github.com/Shilin-LU/VINE)** - W-Bench数据集和失真攻击实现
- **[VideoSeal](https://github.com/facebookresearch/videoseal)** - 视频/图像水印算法




