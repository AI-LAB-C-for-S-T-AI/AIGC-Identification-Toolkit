# Video-Bench: 视频水印鲁棒性评估基准

[![Python 3.8+](https://img.shields.io/badge/python-3.8+-blue.svg)](https://www.python.org/downloads/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Dataset: VideoMarkBench](https://img.shields.io/badge/Dataset-VideoMarkBench-green.svg)](https://www.kaggle.com/datasets/zhengyuanjiang/videomarkbench/data)

> 评估视频水印算法（VideoSeal）在多种视频攻击下的鲁棒性，基于 VideoMarkBench 数据集。

---

## 🚀 快速开始

### 1. 安装依赖

```bash
# 核心依赖
pip install torch torchvision torchaudio

# 评估和可视化依赖
pip install pytorch-msssim lpips scipy pyyaml tqdm matplotlib numpy
```

### 2. 下载数据集

从 Kaggle 下载 VideoMarkBench 数据集：[VideoMarkBench Dataset](https://www.kaggle.com/datasets/zhengyuanjiang/videomarkbench/data)，下载后解压到 `benchmarks/Video-Bench/dataset/VideoMarkBench/`



### 3. 运行评估

```bash
python benchmarks/Video-Bench/run_benchmark.py
```

**结果输出**：`benchmarks/Video-Bench/results/videoseal_robustness/`

---

## 📊 评估流程

### 支持的攻击类型

| 攻击类型 | 强度参数 | 说明 |
|---------|---------|------|
| **Gaussian Noise** | [0.01, 0.05, 0.10, 0.15, 0.20] | 图像级：逐帧添加高斯噪声（σ 越大越强） |
| **Gaussian Blur** | [0.1, 0.5, 1.0, 1.5] | 图像级：逐帧高斯模糊（核标准差 σ） |
| **JPEG Compression** | [90, 80, 60, 40, 20] | 图像级：JPEG压缩，质量越低失真越大 |
| **Crop & Resize** | [0.98, 0.96, 0.94, 0.92, 0.90] | 图像级：裁剪后缩放回原尺寸（保留比例） |
| **Frame Average** | [1, 2, 3, 4, 5] | 视频级：滑动窗口帧平均（窗口越大越平滑） |
| **Frame Swap** | [0.00, 0.05, 0.10, 0.15, 0.20] | 视频级：随机交换相邻帧（概率 p） |
| **Frame Remove** | [0.00, 0.05, 0.10, 0.15, 0.20] | 视频级：随机删除帧（概率 p） |

共计 **7 种攻击 × 多个强度级别**，覆盖空间域与时间域两类典型失真。

### 评估指标

| 指标类别 | 指标 | 判定阈值 | 指标说明 |
|----------|------|----------|----------|
| **质量** | PSNR | ≥ 35.0 dB | Peak Signal-to-Noise Ratio，越高越好 |
| **质量** | SSIM | ≥ 0.95 | Structural Similarity Index，越接近 1 越好 |
| **质量** | tLP | ≤ 0.20 | Temporal LPIPS，衡量跨帧感知一致性，越低越好 |
| **鲁棒性** | FNR | ≤ 0.01 | False Negative Rate，漏检率，越低表示鲁棒性越强 |
| **鲁棒性** | Bit Accuracy | ≥ 0.85 | 解码比特准确率，越高越好 |

---

## 📈 可视化分析

生成雷达图与质量面板便于快速对比：

```bash
python benchmarks/Video-Bench/utils/plot_radar.py \
  benchmarks/Video-Bench/results/videoseal_robustness/metrics.json
```

<table>
  <tr>
    <th>FNR</th>
    <th>Bit Accuracy</th>
    <th>质量评估指标</th>
  </tr>
  <tr>
    <td><img src="results/videoseal_robustness/videoseal_fnr_radar.png" alt="VideoSeal FNR Radar" /></td>
    <td><img src="results/videoseal_robustness/videoseal_bit_accuracy_radar.png" alt="VideoSeal Bit Accuracy Radar" /></td>
    <td style="vertical-align: top; height: 100%;">
      <table>
        <tr><th>指标</th><th>数值</th><th style="white-space: nowrap;">达到阈值</th></tr>
        <tr><td><strong>PSNR</strong></td><td>40.59</td><td>✅</td></tr>
        <tr><td><strong>SSIM</strong></td><td>0.97</td><td>✅</td></tr>
        <tr><td><strong>tLP</strong></td><td>0.001</td><td>✅</td></tr>
      </table>
    </td>
  </tr>
</table>

> 每张雷达图展示 **5 条曲线**，对应 5 个攻击强度等级（从弱到强）。

---


## 🏆 致谢

本项目基于以下开源工作：

- **[VideoMarkBench](https://www.kaggle.com/datasets/zhengyuanjiang/videomarkbench/data)** - 视频攻击实现和评估框架
- **[VideoSeal](https://github.com/facebookresearch/videosse)** - Meta Research 的视频水印算法


---
