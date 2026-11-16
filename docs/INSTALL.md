# Install LongCat-Video-Avatar 1.6

This page is the install guide for the model. The commands are the ones published by the Meituan LongCat Team in [meituan-longcat/LongCat-Video](https://github.com/meituan-longcat/LongCat-Video) and on the [Hugging Face model card](https://huggingface.co/meituan-longcat/LongCat-Video-Avatar-1.6). Run them in the model repository, which is a different checkout from this homepage.

This homepage (`index.html`, `videos/`, `assets/`) does not contain the model. Leave it as a reference while you install.

**Only animate a person who agreed to it.** See [Consent](../README.md#consent).

## What you need

| Item | What the published release specifies |
| --- | --- |
| Operating system | The published commands are a Linux shell with Conda. |
| Python | 3.10, in a Conda environment named `longcat-video`. |
| GPU | The published Avatar 1.6 commands launch **2 GPUs** (`--nproc_per_node=2` and `--context_parallel_size=2`). |
| CUDA | The published PyTorch install is the CUDA 12.4 wheel (`torch==2.6.0+cu124`). The upstream note says to configure the wheel for your CUDA version. |
| VRAM | The Avatar 1.6 model card and the code README do not publish a minimum VRAM figure. See [Hardware](../README.md#hardware). |
| Disk | Hugging Face `usedStorage` on 16 November 2026: **77,623,900,551 bytes** (about 78 GB) for [LongCat-Video-Avatar-1.6](https://huggingface.co/meituan-longcat/LongCat-Video-Avatar-1.6) and **83,310,670,950 bytes** (about 83 GB) for [LongCat-Video](https://huggingface.co/meituan-longcat/LongCat-Video). Plan for both downloads, plus the Conda environment and the videos you generate. |
| Network | The first run downloads PyTorch, FlashAttention, the Python requirements, and both weight repositories. |
| Compiler | `flash_attn==2.7.4.post1` is installed from pip. When a matching wheel is not available, that package builds locally and needs a C++ compiler and a CUDA toolkit that matches the PyTorch build. |

The foundation model is the 13.6B-parameter LongCat-Video model. Avatar 1.6 is the audio-driven checkpoint built on that line. Both weight folders have to sit next to each other, because the inference script loads the tokenizer, the UMT5 text encoder, and the VAE from `../LongCat-Video` relative to the avatar checkpoint.

## 1. Clone the model repository

```shell
git clone --single-branch --branch main https://github.com/meituan-longcat/LongCat-Video
cd LongCat-Video
```

Code license: MIT, Copyright (c) 2025 Meituan. The license does not grant rights to Meituan trademarks or patents. Full text: [LICENSE](https://github.com/meituan-longcat/LongCat-Video/blob/main/LICENSE).

## 2. Create the environment and install PyTorch

```shell
conda create -n longcat-video python=3.10
conda activate longcat-video

pip install torch==2.6.0+cu124 torchvision==0.21.0+cu124 torchaudio==2.6.0 --index-url https://download.pytorch.org/whl/cu124
```

If your driver uses a different CUDA release, install the matching PyTorch 2.6.0 wheel from [the PyTorch index](https://download.pytorch.org/whl/torch/) and keep `torchvision==0.21.0` and `torchaudio==2.6.0` on that same build. The rest of this guide assumes the published CUDA 12.4 wheel.

## 3. Install FlashAttention-2 and the project requirements

```shell
pip install ninja
pip install psutil
pip install packaging
pip install flash_attn==2.7.4.post1

pip install -r requirements.txt

conda install -c conda-forge librosa
conda install -c conda-forge ffmpeg
pip install -r requirements_avatar.txt
```

FlashAttention-2 is the default in the model config. Hopper GPUs (H20, H100, H200, H800) can use FlashAttention-3 instead. The upstream instructions for that switch are in [Usage](USAGE.md#flashattention-3-on-hopper-gpus).

`ffmpeg` is required at runtime. The demo script writes the mp4 by piping raw frames to `ffmpeg` and muxing the source audio.

## 4. Download the weights

Avatar 1.6 inference reads two directories:

* `./weights/LongCat-Video` — tokenizer, UMT5 text encoder, and VAE.
* `./weights/LongCat-Video-Avatar-1.6` — avatar DiT (`base_model` and optional `base_model_int8`), `loraA` (DMD), `loraB` (OPSD), Whisper-large-v3, the scheduler, and the vocal separator.

```shell
pip install "huggingface_hub[cli]"
huggingface-cli download meituan-longcat/LongCat-Video --local-dir ./weights/LongCat-Video
huggingface-cli download meituan-longcat/LongCat-Video-Avatar-1.6 --local-dir ./weights/LongCat-Video-Avatar-1.6
```

Weight license: MIT, as declared on both model cards (`license: mit`). The code README states that the MIT license does not grant rights to use Meituan trademarks or patents.

Keep the folder names above. The script joins `checkpoint_dir` with `..` and then `LongCat-Video`. A renamed parent folder breaks that lookup.

After the download, these paths should exist:

```text
weights/LongCat-Video/tokenizer/
weights/LongCat-Video/text_encoder/
weights/LongCat-Video/vae/
weights/LongCat-Video-Avatar-1.6/base_model/
weights/LongCat-Video-Avatar-1.6/base_model_int8/
weights/LongCat-Video-Avatar-1.6/loraA/lora.safetensors
weights/LongCat-Video-Avatar-1.6/loraB/lora.safetensors
weights/LongCat-Video-Avatar-1.6/whisper-large-v3/
weights/LongCat-Video-Avatar-1.6/vocal_separator/Kim_Vocal_2.onnx
weights/LongCat-Video-Avatar-1.6/scheduler/
```

Older avatar checkpoints (1.0 and 1.5) are separate Hugging Face repositories. You do not need them to run 1.6.

## 5. Check the install

From `LongCat-Video`, with `longcat-video` activated:

```shell
python -c "import torch; print(torch.__version__, torch.cuda.device_count(), torch.cuda.get_device_name(0))"
ffmpeg -version | head -n 1
test -f weights/LongCat-Video-Avatar-1.6/loraB/lora.safetensors && echo "avatar weights: ok"
test -d weights/LongCat-Video/text_encoder && echo "base text encoder: ok"
```

`torch.cuda.device_count()` should be at least 2 before you use the published 2-GPU command. Then follow [Usage](USAGE.md).

## Install problems

| What you see | What to check |
| --- | --- |
| `flash_attn` fails to build | Install a C++ compiler and a CUDA toolkit that matches the PyTorch wheel, then retry `pip install flash_attn==2.7.4.post1`. `ninja`, `psutil`, and `packaging` are installed first in the published steps. |
| `torch.cuda.device_count()` is 0 | The PyTorch wheel does not see a GPU. Confirm the NVIDIA driver, and confirm you installed a CUDA wheel rather than the CPU wheel. |
| Hugging Face download stops or the disk fills | The two repositories are about 78 GB and 83 GB. Free that space on the drive that holds `./weights`, then run the same `huggingface-cli download` command again. The CLI resumes an existing local directory. |
| `FileNotFoundError` for a LoRA or for `LongCat-Video` | The avatar folder must be `./weights/LongCat-Video-Avatar-1.6` and the base folder must be its sibling `./weights/LongCat-Video`. |
| Conda cannot find `ffmpeg` or `librosa` | Use the published channel: `conda install -c conda-forge ffmpeg` and `conda install -c conda-forge librosa`. |
