# LongCat Video Avatar by Transformaiton.com

A customer guide to **LongCat-Video-Avatar 1.6**, the open-source talking-avatar video model from the Meituan LongCat Team (MeiGen-AI). You give it a still photo and a voice recording. It writes a video of that person speaking, singing, or performing.

Transformaiton.com is an AI consultancy for small businesses. This repository is our public copy of the MeiGen-AI project homepage, plus this English guide. The interactive page is still [`index.html`](index.html). Open that file in a browser to play the original demos. The model code and the weights are published separately, and the links are below.

[Website](https://transformaiton.com/) · [info@transformaiton.com](mailto:info@transformaiton.com) · [GitHub](https://github.com/Transformaiton) · [Install](docs/INSTALL.md) · [Usage](docs/USAGE.md)

[![Project page](https://img.shields.io/badge/Project-Page-green)](https://meigen-ai.github.io/LongCat-Video-Avatar-1.6-Page)
[![Technique report](https://img.shields.io/badge/Technique-Report-red)](https://github.com/meituan-longcat/LongCat-Video/blob/main/assets/LongCat-Video-Avatar-1.6-Tech-Report.pdf)
[![Code](https://img.shields.io/badge/Code-GitHub-black?logo=github)](https://github.com/meituan-longcat/LongCat-Video)
[![Hugging Face](https://img.shields.io/badge/%F0%9F%A4%97%20Hugging%20Face-Model-blue)](https://huggingface.co/meituan-longcat/LongCat-Video-Avatar-1.6)
[![License](https://img.shields.io/badge/License-MIT-f5de53?&color=f5de53)](LICENSE)

## Consent

Only animate a person who agreed to it. That covers the face in the photo and the voice in the audio. A client, employee, or anyone else needs to agree before you put them on camera this way. If they withdraw permission, stop using that portrait and that audio. The MIT license on the model is a software license. It does not give you rights in someone else's likeness or recording.

The same rule applies if you make the soundtrack with [VoiceStudio](https://github.com/Transformaiton/VoiceStudio): clone only a voice you own, or a voice whose owner gave permission.

## What the model does

LongCat-Video-Avatar 1.6 is an audio-driven human video model. It is the 1.6 update of the LongCat-Video-Avatar line, built on the LongCat-Video foundation model (13.6 billion parameters). Version 1.6 keeps the Avatar 1.5 architecture, including the Whisper-large-v3 audio encoder, and adds two task LoRAs plus inference optimizations.

The Meituan LongCat Team describes three changes from 1.5, in the model card and in the README of this homepage:

- More natural and stable lip animation: less exaggerated or strained mouth movement, less overly red lip color, and less flicker around the mouth.
- Long-video generation that limits error buildup, with published results past 10 minutes while holding visual quality, identity, and temporal coherence.
- Faster end-to-end inference, including an 8-step distilled path.

One model run can do audio-text-to-video, audio-image-to-video (a photo plus audio), and video continuation. Single-person and two-person audio are both in the published scripts. The distilled 1.6 path generates at 25 frames per second. Detail, commands, and frame counts are in [Usage](docs/USAGE.md).

The authors' technique report: [LongCat-Video-Avatar 1.6 Technical Report (PDF)](https://github.com/meituan-longcat/LongCat-Video/blob/main/assets/LongCat-Video-Avatar-1.6-Tech-Report.pdf).

### Published human evaluation

The numbers below are the Meituan LongCat Team's published study, quoted from their project README and shown in their chart. Transformaiton.com did not run this study and has not reproduced these scores.

They compare LongCat-Video-Avatar 1.6 with LongCat-Video-Avatar 1.5 and four commercial systems: OmniHuman-2.0, HeyGen, OmniHuman-1.5, and Kling Avatar 2.0. The all-pairs preference study has 33,051 valid individual votes across 15 model pairings. LongCat-Video-Avatar 1.6 ranks second of the six systems, with a Bradley–Terry Elo of 1068.1. It ranks behind OmniHuman-2.0 and ahead of LongCat-Video-Avatar 1.5, HeyGen, OmniHuman-1.5, and Kling Avatar 2.0.

![Avatar Arena Leaderboard from the MeiGen-AI project page. Bradley–Terry Elo from 33,051 pairwise human-preference battles. LongCat-Video-Avatar 1.6 is rank 2 with Elo 1068.1.](assets/fig.png)

## Small-business use

A typical job is a spokesperson video: one consented photo, one consented voice track, and a short prompt that describes the person, the clothes, and the room. The photo keeps identity. The audio drives the mouth and the performance. The prompt describes the scene.

Ways a small business can use a photo and a voice track:

- **Spokesperson and founder videos.** A photo of the owner, plus a narration, for a homepage, a proposal, or a sales page.
- **Product and service explainers.** The same portrait, with a new script each time the offer changes.
- **Training and how-to clips.** A consistent on-camera person for lessons, without booking a shoot for every rewrite.
- **Ads and social posts.** Short spoken updates, in a look you can repeat.
- **Two-person scenes.** The published multi-person path, when both people have agreed.

Pair the picture with a voice you control. [VoiceStudio by Transformaiton.com](https://github.com/Transformaiton/VoiceStudio) is a local voice studio: record a short clip you have permission to use, type the new line, and export a WAV. Point Avatar 1.6 at that WAV. VoiceStudio stays on your machine. This repository does not include a combined demo of the two tools, and it does not claim a measured quality score for that pairing.

Write the prompt the way the authors recommend for this architecture: a full sentence about who is on camera, what they wear, and where they are. Their example is "A young woman with long black hair is speaking and smiling, wearing a white blouse, sitting in a bright café."

## Demos already in this repository

These clips and the chart are the files MeiGen-AI shipped on the project page. They are linked here. Nothing new was generated for this guide. Click a link to play the file on GitHub, or open [`index.html`](index.html) locally for the full page with audio controls.

Introduction (about 80 seconds), from the page section "Introduction Video":

[videos/introduction/2.mp4](videos/introduction/2.mp4)

Long generations, from the page section on reduced error accumulation. Durations measured from the files in this repository:

| Clip | Length | File |
| --- | ---: | --- |
| Long video 1 | 9 min 42 sec | [videos/long_video/1_v2.mp4](videos/long_video/1_v2.mp4) |
| Long video 2 | 9 min 5 sec | [videos/long_video/2_v2.mp4](videos/long_video/2_v2.mp4) |
| Long video 3 | 10 min 5 sec | [videos/long_video/3_v2.mp4](videos/long_video/3_v2.mp4) |
| Long video 4 | 10 min 5 sec | [videos/long_video/4.mp4](videos/long_video/4.mp4) |
| Long video 5 | 10 min 5 sec | [videos/long_video/5.mp4](videos/long_video/5.mp4) |

Speech, from the page's creative showcase. The file is the one already in this repository (about 10 seconds):

<video src="videos/creative/speech/1.mp4" controls playsinline width="720"></video>

[videos/creative/speech/1.mp4](videos/creative/speech/1.mp4)

Creative categories, using the page's own labels. Two examples each:

| Page category | Clips |
| --- | --- |
| Speech | [speech/1.mp4](videos/creative/speech/1.mp4) (10 sec) · [speech/2.mp4](videos/creative/speech/2.mp4) (23 sec) |
| Music | [music/1.mp4](videos/creative/music/1.mp4) (12 sec) · [music/2.mp4](videos/creative/music/2.mp4) (18 sec) |
| Performance | [performance/1.mp4](videos/creative/performance/1.mp4) · [performance/2.mp4](videos/creative/performance/2.mp4) |
| Expression | [expression/1.mp4](videos/creative/expression/1.mp4) · [expression/2.mp4](videos/creative/expression/2.mp4) |
| Live streaming | [live/1.mp4](videos/creative/live/1.mp4) · [live/2.mp4](videos/creative/live/2.mp4) |
| Animation | [animation/1.mp4](videos/creative/animation/1.mp4) · [animation/2.mp4](videos/creative/animation/2.mp4) |
| Interaction | [interaction/1.mp4](videos/creative/interaction/1.mp4) · [interaction/2.mp4](videos/creative/interaction/2.mp4) |

Version comparison (1.5 beside 1.6, matched inputs on the page): [1.5](videos/comparison_1.5/vc55_v1d5.mp4) and [1.6](videos/comparison_1.5/vc55_v1d6.mp4), plus [case 3](videos/comparison_1.5/case3_v1d6.mp4), [case 4](videos/comparison_1.5/case4_v1d6.mp4) (41 sec), and [vc69](videos/comparison_1.5/vc69_v1d6.mp4). Each 1.5 file sits next to its 1.6 file in [`videos/comparison_1.5/`](videos/comparison_1.5).

Side-by-side with other systems, for the page's commercial comparison (LongCat, OmniHuman-2.0, HeyGen, Kling Avatar 2.0, OmniHuman-1.5): [`videos/comparison_other_model/`](videos/comparison_other_model). One LongCat example from that set is [longcat_54.mp4](videos/comparison_other_model/longcat_54.mp4).

The page's background loop is [videos/background/background.mp4](videos/background/background.mp4).

## Where the code and weights live

This repository is the **project homepage** (MIT, Copyright (c) 2026 Meituan). It holds `index.html`, the demo media, and this guide. It does not hold the inference code or the checkpoints.

The official project page links the model code to the Meituan LongCat GitHub organization. That is the repository to clone. MeiGen-AI's GitHub organization hosts the homepage (`MeiGen-AI/LongCat-Video-Avatar-1.6-Page`), which is the project this copy came from.

| What | Where | License |
| --- | --- | --- |
| Inference code, demo scripts, and the technique-report PDF | [github.com/meituan-longcat/LongCat-Video](https://github.com/meituan-longcat/LongCat-Video) | MIT. Copyright (c) 2025 Meituan. [LICENSE](https://github.com/meituan-longcat/LongCat-Video/blob/main/LICENSE). No rights to Meituan trademarks or patents. |
| Avatar 1.6 weights (DiT, DMD LoRA, OPSD LoRA, INT8 DiT, Whisper-large-v3, vocal separator) | [huggingface.co/meituan-longcat/LongCat-Video-Avatar-1.6](https://huggingface.co/meituan-longcat/LongCat-Video-Avatar-1.6) | MIT (`license: mit` on the model card). |
| Base LongCat-Video weights (tokenizer, UMT5 text encoder, VAE). Required as a sibling folder. | [huggingface.co/meituan-longcat/LongCat-Video](https://huggingface.co/meituan-longcat/LongCat-Video) | MIT (`license: mit` on the model card). |
| Published homepage | [meigen-ai.github.io/LongCat-Video-Avatar-1.6-Page](https://meigen-ai.github.io/LongCat-Video-Avatar-1.6-Page) | Same homepage project. Upstream source: [MeiGen-AI/LongCat-Video-Avatar-1.6-Page](https://github.com/MeiGen-AI/LongCat-Video-Avatar-1.6-Page). |
| Earlier avatar homepage | [github.com/MeiGen-AI/LongCat-Video-Avatar](https://github.com/MeiGen-AI/LongCat-Video-Avatar) | The 1.0 project page. The 1.6 code to run is `meituan-longcat/LongCat-Video`. |

Author contact published on the model card: longcat-team@meituan.com.

## Hardware

| Requirement | Source |
| --- | --- |
| NVIDIA GPUs and a CUDA build of PyTorch. The published install command is `torch==2.6.0+cu124` (CUDA 12.4). Match the wheel to your CUDA version if it differs. | Model README install section. |
| **2 GPUs** for every published Avatar 1.6 command (`--nproc_per_node=2` and `--context_parallel_size=2`). For more than two GPUs, set both numbers to the GPU count and export `LONGCAT_A2A_WORKSPACE=1`. | Model card and code README. |
| Python 3.10, Conda, ffmpeg, and FlashAttention-2 (`flash_attn==2.7.4.post1`). | Model README. |
| Resolutions the script accepts: `480p` (480×832, the default) and `720p` (768×1280). | `run_demo_avatar_single_audio_to_video.py`. |
| Optional `--use_int8` loads the weight-only INT8 DiT. The script's help text calls this reduced VRAM use. `--use_fp8` is separate and needs NVIDIA Transformer Engine. Do not enable both. | Model card and the demo script. |
| Hopper GPUs named for FlashAttention-3: H20, H100, H200, H800. | Model card. |
| Disk for weights: about **78 GB** for the avatar repository (Hugging Face `usedStorage` 77,623,900,551 bytes) and about **83 GB** for the base repository (83,310,670,950 bytes), read on 16 November 2026. Those totals include every file in each repo, including the INT8 weights and the extra Whisper copies. Leave room for the environment and for output videos. | Hugging Face model API. |
| **A minimum VRAM figure is not published** on the Avatar 1.6 model card or in the code README. Treat the 2-GPU command as the supported example, and use INT8 or 480p when a card runs out of memory. This guide does not invent a card size or a gigabyte requirement. | Model card and code README. |

CPU-only inference is not a published path. The install command is a CUDA PyTorch wheel, and the script initializes NCCL.

## Quick start

Full commentary is in [docs/INSTALL.md](docs/INSTALL.md) and [docs/USAGE.md](docs/USAGE.md). This is the published photo-plus-audio path, using the example that ships inside the model repository.

1. Clone the model code (not this homepage):

```shell
git clone --single-branch --branch main https://github.com/meituan-longcat/LongCat-Video
cd LongCat-Video
```

2. Create the environment and install the published dependencies:

```shell
conda create -n longcat-video python=3.10
conda activate longcat-video

pip install torch==2.6.0+cu124 torchvision==0.21.0+cu124 torchaudio==2.6.0 --index-url https://download.pytorch.org/whl/cu124

pip install ninja
pip install psutil
pip install packaging
pip install flash_attn==2.7.4.post1

pip install -r requirements.txt
conda install -c conda-forge librosa
conda install -c conda-forge ffmpeg
pip install -r requirements_avatar.txt
```

3. Download both weight repositories into the layout the script expects:

```shell
pip install "huggingface_hub[cli]"
huggingface-cli download meituan-longcat/LongCat-Video --local-dir ./weights/LongCat-Video
huggingface-cli download meituan-longcat/LongCat-Video-Avatar-1.6 --local-dir ./weights/LongCat-Video-Avatar-1.6
```

4. Render the shipped single-person example (image plus audio, 2 GPUs, distilled 1.6):

```shell
torchrun --nproc_per_node=2 run_demo_avatar_single_audio_to_video.py \
  --context_parallel_size=2 \
  --checkpoint_dir=./weights/LongCat-Video-Avatar-1.6 \
  --stage_1=ai2v \
  --input_json=assets/avatar/single_example_1.json \
  --use_distill \
  --lora_path loraB/lora.safetensors \
  --model_type avatar-v1.6 \
  --mix_steps=4 \
  --mix_alpha=1.0
```

5. Open `outputs_avatar_single/ai2v_demo_1.mp4`.

That example belongs to the model repository. It is their stage prompt, their PNG, and their MP3. To use your own files, copy the JSON shape and change the paths. Both the person in the photo and the person in the audio need to have agreed.

```json
{
  "prompt": "A person speaks to the camera in a bright office, wearing a navy blazer. The camera is steady. They explain a service clearly, with natural head movement.",
  "cond_image": "inputs/portrait.png",
  "cond_audio": {
    "person1": "inputs/voice.wav"
  }
}
```

Pass that file with `--input_json`. Step-by-step variants (text-only, longer continuation, two people, your own VoiceStudio WAV) are in [docs/USAGE.md](docs/USAGE.md).

## Troubleshooting and FAQ

| Question | Answer |
| --- | --- |
| Is the model inside this GitHub repository? | The homepage and this guide are here. Clone [meituan-longcat/LongCat-Video](https://github.com/meituan-longcat/LongCat-Video) and download the two Hugging Face repositories listed above. |
| Which command is the photo-plus-voice one? | `--stage_1=ai2v` with a JSON file that sets `cond_image` and `cond_audio.person1`. |
| How long is one segment? | The 1.6 script uses 93 frames at 25 fps, about 3.7 seconds. Extra continuation segments add 80 frames each, about 3.2 seconds. The formula and the 5-segment example are in [Usage](docs/USAGE.md#longer-clips). |
| The run says `No vocal detected`. | The vocal separator found no speech or singing. Use a take with a clear voice. |
| The process is killed or CUDA reports out of memory. | No VRAM minimum is published. Try `--use_int8` and `--resolution 480p`. The documented multi-GPU switch is `--context_parallel_size` equal to `--nproc_per_node`. |
| The first run with compile flags is much slower. | The model card describes that warm-up. Later runs with the same shapes reuse the compiled graphs. |
| Can this run on a laptop CPU? | The published install is a CUDA build, and the script starts NCCL. There is no published CPU command. |
| May we use the videos commercially? | The code and the weights are MIT, which allows commercial use of the software, with the copyright and license notice kept in place. The license does not grant Meituan trademark or patent rights. You still need the right to use the person's face and voice, and you still have to follow the privacy and content laws that apply to you. The model card says the same thing about downstream responsibility. |
| Who do we ask? | This guide: [info@transformaiton.com](mailto:info@transformaiton.com). The model itself: longcat-team@meituan.com and [meituan-longcat/LongCat-Video issues](https://github.com/meituan-longcat/LongCat-Video/issues). |

More failure cases (missing LoRAs, `ffmpeg`, FP8, two-person audio) are in [docs/USAGE.md](docs/USAGE.md#troubleshooting) and [docs/INSTALL.md](docs/INSTALL.md#install-problems).

## Contact

Transformaiton.com is an AI consultancy for small businesses. This repository is the LongCat-Video-Avatar homepage we share with customers, together with the install and usage notes above.

| | |
| --- | --- |
| Website | [https://transformaiton.com/](https://transformaiton.com/) |
| Email | [info@transformaiton.com](mailto:info@transformaiton.com) |
| GitHub | [https://github.com/Transformaiton](https://github.com/Transformaiton) |
| This copy | [https://github.com/Transformaiton/LongCat-Video-Avatar-1.6-Page](https://github.com/Transformaiton/LongCat-Video-Avatar-1.6-Page) |
| Voice clone guide | [https://github.com/Transformaiton/VoiceStudio](https://github.com/Transformaiton/VoiceStudio) |

Questions about installing this copy, running the published commands, or using a VoiceStudio WAV as the avatar soundtrack: email info@transformaiton.com.

## Credits

LongCat-Video-Avatar 1.6 is the work of the **Meituan LongCat Team**, published through **MeiGen-AI**.

- Project page: [https://meigen-ai.github.io/LongCat-Video-Avatar-1.6-Page](https://meigen-ai.github.io/LongCat-Video-Avatar-1.6-Page)
- Homepage source this repository copies: [https://github.com/MeiGen-AI/LongCat-Video-Avatar-1.6-Page](https://github.com/MeiGen-AI/LongCat-Video-Avatar-1.6-Page)
- MeiGen-AI on GitHub: [https://github.com/MeiGen-AI](https://github.com/MeiGen-AI)
- Model code: [https://github.com/meituan-longcat/LongCat-Video](https://github.com/meituan-longcat/LongCat-Video)
- Weights: [https://huggingface.co/meituan-longcat/LongCat-Video-Avatar-1.6](https://huggingface.co/meituan-longcat/LongCat-Video-Avatar-1.6)
- Base model: [https://huggingface.co/meituan-longcat/LongCat-Video](https://huggingface.co/meituan-longcat/LongCat-Video)
- Technique report: [LongCat-Video-Avatar-1.6-Tech-Report.pdf](https://github.com/meituan-longcat/LongCat-Video/blob/main/assets/LongCat-Video-Avatar-1.6-Tech-Report.pdf)

The model card also thanks the contributors to [Wan](https://huggingface.co/Wan-AI), [UMT5-XXL](https://huggingface.co/google/umt5-xxl), [Diffusers](https://github.com/huggingface/diffusers), and [Hugging Face](https://huggingface.co).

Demo videos, `assets/fig.png`, and `index.html` are theirs. Durations quoted above were measured from the copies of those files in this repository.

### Affiliation

Transformaiton.com is not affiliated with or endorsed by MeiGen-AI or Meituan. Meituan, LongCat, and MeiGen-AI are their names. Transformaiton.com uses the public MIT release and credits the authors.

### License and copyright

The homepage license is unchanged. See [LICENSE](LICENSE):

> MIT License
>
> Copyright (c) 2026 Meituan

The model code is separately MIT, Copyright (c) 2025 Meituan. The weight repositories state MIT on their model cards. Keep those copyright notices and permission notices in any copy. Nothing in this guide changes those licenses.

The authors' citation request, from the Avatar 1.6 model card:

```text
@misc{meituanlongcatteam2026longcatvideoavatar16technicalreport,
      title={LongCat-Video-Avatar 1.6 Technical Report},
      author={Meituan LongCat Team},
      year={2026},
      archivePrefix={arXiv},
      primaryClass={cs.CV}
}
```

### Modifications by Transformaiton.com

16 November 2026. Transformaiton.com rewrote this README as an English customer guide and added [docs/INSTALL.md](docs/INSTALL.md) and [docs/USAGE.md](docs/USAGE.md).

Unchanged in that edit: [LICENSE](LICENSE) and its copyright, `index.html`, the files under `videos/`, and the files under `assets/`. The homepage still opens the same way. This guide does not modify the model, the weights, or the upstream inference scripts.
