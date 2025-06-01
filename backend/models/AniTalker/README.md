# AniTalker - Complete Installation & Usage Guide

## Overview
AniTalker is a state-of-the-art AI model that creates vivid and diverse talking faces through identity-decoupled facial motion encoding. This guide provides step-by-step instructions for installation, model setup, and inference.

## 🚀 Quick Links
- **Paper**: [arXiv:2405.03121](https://arxiv.org/abs/2405.03121)
- **Project Page**: [AniTalker](https://x-lance.github.io/AniTalker/)
- **Demo**: [Hugging Face Space](https://huggingface.co/spaces/Delik/Anitalker)
- **Colab**: [Try it now](https://colab.research.google.com/github/yuhanxu01/AniTalker/blob/main/AniTalker_demo.ipynb)

## 📋 Prerequisites
- Python 3.9.0
- CUDA-compatible GPU (recommended)
- At least 8GB GPU memory
- 10GB+ free disk space

## 🛠️ Installation

### Step 1: Clone Repository
```bash
git clone https://github.com/X-LANCE/AniTalker.git
cd AniTalker
```

### Step 2: Create Conda Environment
```bash
conda create -n anitalker python==3.9.0
conda activate anitalker
```

### Step 3: Install PyTorch
```bash
conda install pytorch==1.8.0 torchvision==0.9.0 torchaudio==0.8.0 cudatoolkit=11.1 -c pytorch -c conda-forge
```

### Step 4: Install Dependencies
```bash
pip install -r requirements.txt
pip install transformers==4.19.2
```

### Step 5: Optional - Face Super-Resolution
For 512x512 output resolution:
```bash
pip install facexlib
pip install tb-nightly -i https://mirrors.aliyun.com/pypi/simple
pip install gfpgan
```

## 📦 Model Downloads

### Download Pretrained Weights
Create the checkpoints directory and download models:

```bash
mkdir ckpts
```

**Option 1: Hugging Face (Recommended)**
Download from [Hugging Face Repository](https://huggingface.co/taocode/anitalker_ckpts/tree/main)

**Option 2: Baidu Drive (For Chinese Users)**
- URL: [Baidu Drive](https://pan.baidu.com/s/1gqTPmoJ3QwKbGkqgMXM3Jw?pwd=antk)
- Password: `antk`

### Required Model Structure
```
ckpts/
├── chinese-hubert-large/
│   ├── config.json
│   ├── preprocessor_config.json
│   └── pytorch_model.bin
├── stage1.ckpt
├── stage2_pose_only_mfcc.ckpt
├── stage2_full_control_mfcc.ckpt
├── stage2_audio_only_hubert.ckpt
├── stage2_pose_only_hubert.ckpt
└── stage2_full_control_hubert.ckpt
```

## 🎯 Model Selection Guide

| Model | Audio Features | Control | Recommendation |
|-------|---------------|---------|----------------|
| `stage2_audio_only_hubert.ckpt` | Hubert | None | ⭐ **Recommended for beginners** |
| `stage2_pose_only_hubert.ckpt` | Hubert | Head Pose | Good for pose control |
| `stage2_full_control_hubert.ckpt` | Hubert | Pose + Location + Scale | Maximum control |
| `stage2_pose_only_mfcc.ckpt` | MFCC | Head Pose | ❌ **Not recommended** |
| `stage2_full_control_mfcc.ckpt` | MFCC | Pose + Location + Scale | ❌ **Not recommended** |

## 🎬 Running Inference

### Basic Usage (Recommended)
Use the Hubert audio-only model for best results:

```bash
python ./code/demo.py \
    --infer_type 'hubert_audio_only' \
    --stage1_checkpoint_path 'ckpts/stage1.ckpt' \
    --stage2_checkpoint_path 'ckpts/stage2_audio_only_hubert.ckpt' \
    --test_image_path 'test_demos/portraits/monalisa.jpg' \
    --test_audio_path 'test_demos/audios/monalisa.wav' \
    --test_hubert_path 'test_demos/audios_hubert/monalisa.npy' \
    --result_path 'outputs/monalisa_hubert/'
```

### With Pose Control
```bash
python ./code/demo.py \
    --infer_type 'hubert_pose_only' \
    --stage1_checkpoint_path 'ckpts/stage1.ckpt' \
    --stage2_checkpoint_path 'ckpts/stage2_pose_only_hubert.ckpt' \
    --test_image_path 'test_demos/portraits/monalisa.jpg' \
    --test_audio_path 'test_demos/audios/monalisa.wav' \
    --test_hubert_path 'test_demos/audios_hubert/monalisa.npy' \
    --result_path 'outputs/monalisa_pose/' \
    --control_flag \
    --pose_yaw 0.25 \
    --pose_pitch 0 \
    --pose_roll 0
```

### With Face Super-Resolution (512x512)
Add the `--face_sr` flag to any command:
```bash
python ./code/demo.py \
    --infer_type 'hubert_audio_only' \
    --stage1_checkpoint_path 'ckpts/stage1.ckpt' \
    --stage2_checkpoint_path 'ckpts/stage2_audio_only_hubert.ckpt' \
    --test_image_path 'test_demos/portraits/monalisa.jpg' \
    --test_audio_path 'test_demos/audios/monalisa.wav' \
    --test_hubert_path 'test_demos/audios_hubert/monalisa.npy' \
    --result_path 'outputs/monalisa_512/' \
    --face_sr
```

## 🎨 Web UI
Launch the web interface for easier usage:
```bash
python code/webgui.py
```

## 📁 Input Requirements

### Image Requirements
- Format: JPG, PNG
- Resolution: Any (will be processed to 256x256)
- Content: Single face, centered, front-facing preferred
- Quality: High resolution recommended

### Audio Requirements
- Format: WAV (recommended)
- Language: English (best performance)
- Quality: Clear speech, minimal background noise
- Duration: Any length

## ⚙️ Key Parameters

| Parameter | Description | Default | Range |
|-----------|-------------|---------|-------|
| `--pose_yaw` | Head rotation left/right | 0 | -1.0 to 1.0 |
| `--pose_pitch` | Head tilt up/down | 0 | -1.0 to 1.0 |
| `--pose_roll` | Head roll left/right | 0 | -1.0 to 1.0 |
| `--seed` | Random seed for reproducibility | 0 | Any integer |
| `--face_sr` | Enable super-resolution | False | True/False |

## 💡 Best Practices

### 1. **Image Selection**
- Keep the head centered in the frame
- Use front-facing portraits for best results
- Ensure good lighting and high image quality

### 2. **Audio Quality**
- Use English speech for optimal performance
- Ensure clear pronunciation and minimal background noise
- Normal speaking pace works best

### 3. **Pose Control**
- Keep generated poses similar to the original portrait
- For frontal faces, keep pose values near 0
- Gradual pose changes work better than dramatic ones

### 4. **Performance Tips**
- Start with `stage2_audio_only_hubert.ckpt` model
- Use controllable models only when specific control is needed
- Enable face super-resolution for higher quality output

## 🐛 Troubleshooting

### Common Issues

**1. CUDA Out of Memory**
- Reduce batch size or use CPU inference
- Close other GPU applications

**2. Model Download Issues**
- Check internet connection
- Try alternative download links
- Verify file integrity after download

**3. Audio Processing Errors**
- Ensure audio files are in WAV format
- Check if Hubert features are properly extracted

**4. Poor Results**
- Try different seed values
- Adjust pose parameters gradually
- Ensure input image meets requirements

### Platform-Specific Guides
- **Windows**: [Windows Tutorial](md_docs/run_on_windows.md)
- **macOS**: [macOS Tutorial](md_docs/run_on_macOS.md)

## 📊 Output Information
- **Resolution**: 256x256 (512x512 with super-resolution)
- **Format**: MP4 video
- **Location**: Specified in `--result_path`
- **Naming**: `{image_name}-{audio_name}.mp4`

## 🔄 Updates & Community
- Check the [GitHub repository](https://github.com/X-LANCE/AniTalker) for latest updates
- Join discussions in GitHub Issues
- Contribute to the project via Pull Requests

## ⚠️ Limitations
- Primarily trained on English speech
- Works best with frontal face images
- Limited to head and face animation
- May have biases based on training data demographics

