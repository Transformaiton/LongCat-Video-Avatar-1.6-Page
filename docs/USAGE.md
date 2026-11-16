# Run LongCat-Video-Avatar 1.6

Run these commands inside the model checkout, [meituan-longcat/LongCat-Video](https://github.com/meituan-longcat/LongCat-Video), after [Install](INSTALL.md). The flags below are the ones in that repository's README and in `run_demo_avatar_single_audio_to_video.py` / `run_demo_avatar_multi_audio_to_video.py` on the `main` branch.

**Only animate a person who agreed to it.** A portrait and a voice recording are personal data. Get agreement before you generate, and stop using both if that person withdraws it.

This guide describes the published commands and the current demo scripts. It does not add new sample outputs. The clips in the homepage repository are MeiGen-AI's own demo files; see the [README demos](../README.md#demos-already-in-this-repository).

## What one run does

Avatar 1.6 takes a text prompt plus audio, and optionally a still image, and writes a talking or performing video.

The single-person script supports three jobs:

| Job | Flag | Inputs |
| --- | --- | --- |
| Audio-text-to-video | `--stage_1=at2v` | Prompt and one audio file. The picture is generated from the text. |
| Audio-image-to-video | `--stage_1=ai2v` | Prompt, one audio file, and one reference image. This is the photo-plus-voice path. |
| Video continuation | `--num_segments` greater than 1 | The same inputs, then further segments that continue the clip. |

With `--model_type avatar-v1.6` the script uses the Avatar 1.5 architecture, Whisper-large-v3 for audio, and **25 frames per second**. Each segment is **93 frames** (about 3.72 seconds). A continuation segment reuses **13** conditioning frames, so each extra segment adds **80 frames** (about 3.2 seconds). Those counts are what the current single-person script sets for this model type.

`--use_distill` is required for the published 1.6 commands. When it is set, the script runs **8 denoising steps** and sets text guidance and audio guidance to **1.0**.

## Published quick start: their example clip

This uses the example JSON shipped in the model repository (`assets/avatar/single_example_1.json`): a stage prompt, `assets/avatar/single/man.png`, and `assets/avatar/single/man.mp3`.

```shell
conda activate longcat-video
cd LongCat-Video

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

The file is written to:

```text
outputs_avatar_single/ai2v_demo_1.mp4
```

`ffmpeg` muxes the source audio onto the frames. One segment is the default (`--num_segments=1`).

## Your own photo and voice

1. Get permission from the person in the photo and from the person whose voice is in the audio. They can be the same person.
2. Put a still image and an audio file somewhere under the model repository. The published examples use PNG with MP3, and WAV for the multi-person sample.
3. Write a JSON file. Paths are resolved from the directory where you launch `torchrun`.

`inputs/spokesperson.json`:

```json
{
  "prompt": "A person speaks to the camera in a bright office, wearing a navy blazer. The camera is steady. They explain a service clearly, with natural head movement.",
  "cond_image": "inputs/portrait.png",
  "cond_audio": {
    "person1": "inputs/voice.wav"
  }
}
```

Describe appearance, clothing, setting, and the speaking action in the prompt. The upstream 1.5 notes, which apply to this same architecture, say a longer description holds identity and motion more steadily than a short one. Their example of that style is: "A young woman with long black hair is speaking and smiling, wearing a white blouse, sitting in a bright café."

4. Generate:

```shell
torchrun --nproc_per_node=2 run_demo_avatar_single_audio_to_video.py \
  --context_parallel_size=2 \
  --checkpoint_dir=./weights/LongCat-Video-Avatar-1.6 \
  --stage_1=ai2v \
  --input_json=inputs/spokesperson.json \
  --resolution 480p \
  --use_distill \
  --lora_path loraB/lora.safetensors \
  --model_type avatar-v1.6 \
  --mix_steps=4 \
  --mix_alpha=1.0
```

`--resolution` accepts `480p` (480×832, the script default) or `720p` (768×1280).

### Voice from VoiceStudio

[VoiceStudio](https://github.com/Transformaiton/VoiceStudio) is Transformaiton.com's guide to a local voice studio. It can turn a short recording you have permission to use into a new spoken line, then export a WAV. Use that WAV as `cond_audio.person1`.

Record or upload only your own voice, or a voice whose owner agreed. VoiceStudio's own rule is the same. The avatar model then animates a photo the person also agreed to. The two repositories are separate installs. This page does not ship a combined sample of that pairing.

## Audio-text-to-video

No reference image. The published command:

```shell
torchrun --nproc_per_node=2 run_demo_avatar_single_audio_to_video.py \
  --context_parallel_size=2 \
  --checkpoint_dir=./weights/LongCat-Video-Avatar-1.6 \
  --stage_1=at2v \
  --input_json=assets/avatar/single_example_1.json \
  --use_distill \
  --lora_path loraB/lora.safetensors \
  --model_type avatar-v1.6 \
  --mix_steps=4 \
  --mix_alpha=1.0
```

Output: `outputs_avatar_single/at2v_demo_1.mp4`.

The JSON still has a `cond_image` field in the shipped example. The `at2v` branch uses the prompt and `cond_audio.person1`.

## Longer clips

Continuation is how the release produces long videos. The model card describes generation past 10 minutes by reducing error accumulation across autoregressive segments. The clips in this homepage under `videos/long_video/` run about 9.1 to 10.1 minutes (545 to 605 seconds, measured from those files).

The published single-person continuation command uses five segments:

```shell
torchrun --nproc_per_node=2 run_demo_avatar_single_audio_to_video.py \
  --context_parallel_size=2 \
  --checkpoint_dir=./weights/LongCat-Video-Avatar-1.6 \
  --stage_1=ai2v \
  --input_json=assets/avatar/single_example_1.json \
  --num_segments=5 \
  --ref_img_index=10 \
  --mask_frame_range=0 \
  --use_distill \
  --lora_path loraB/lora.safetensors \
  --model_type avatar-v1.6 \
  --mix_steps=4 \
  --mix_alpha=1.0
```

Replace `--stage_1=ai2v` with `--stage_1=at2v` for text-only continuation. The same `--num_segments=5 --ref_img_index=10 --mask_frame_range=0` pair is what the upstream README publishes for that mode.

Length of a 1.6 single-person run, from the frame counts in the script:

```text
seconds ≈ 93/25 + (num_segments - 1) × 80/25
```

Five segments are about 16.5 seconds. The script writes the finished continuation to `outputs_avatar_single/video_continue_<N>.mp4`, where `<N>` is the last segment index plus one. Add `--save_every_segment` to also write the cumulative file after each segment. The default saves the final file.

The script separates vocals and then builds audio embeddings for the generated duration. A short source file is zero-padded to that duration. Match the audio length to the video you expect, so the speaker is not followed by a long silence.

Upstream tips for repeated motion, from the Avatar 1.5 notes on this architecture: `--ref_img_index` between 0 and 24 favors consistency, and 30 is described as reducing repeated actions. Raising `--mask_frame_range` is also described as reducing repeated actions, with a warning that a large value can add artifacts. The published 1.6 continuation command uses `--ref_img_index=10` and `--mask_frame_range=0`, and 0 is the script default.

## Two people

The multi-person script reads two audio files and one image. The shipped example is `assets/avatar/multi_example_1.json` (`audio_type` `para`, two WAV files, one PNG).

```shell
torchrun --nproc_per_node=2 run_demo_avatar_multi_audio_to_video.py \
  --context_parallel_size=2 \
  --checkpoint_dir=./weights/LongCat-Video-Avatar-1.6 \
  --input_json=assets/avatar/multi_example_1.json \
  --use_distill \
  --lora_path loraB/lora.safetensors \
  --model_type avatar-v1.6 \
  --mix_steps=4 \
  --mix_alpha=1.0
```

Output directory default: `outputs_avatar_multi/ai2v_demo_1.mp4`.

Continuation:

```shell
torchrun --nproc_per_node=2 run_demo_avatar_multi_audio_to_video.py \
  --context_parallel_size=2 \
  --checkpoint_dir=./weights/LongCat-Video-Avatar-1.6 \
  --input_json=assets/avatar/multi_example_1.json \
  --num_segments=5 \
  --ref_img_index=10 \
  --mask_frame_range=0 \
  --use_distill \
  --lora_path loraB/lora.safetensors \
  --model_type avatar-v1.6 \
  --mix_steps=4 \
  --mix_alpha=1.0
```

`audio_type` in the JSON:

| Value | Behavior in the script and the upstream notes |
| --- | --- |
| `para` | Merge. The two clips are summed. The upstream notes say the clips need equal length. This is the default when the field is omitted. |
| `add` | Concatenate. The notes say equal length is not required: the clips are placed in sequence, with silence padding for gaps, person 1 first and person 2 after. |

Both people need to have agreed to appear and to have their voices used.

## LoRA mixing, INT8, and FP8

Avatar 1.6 ships two adapters:

* `loraA/lora.safetensors` — DMD LoRA.
* `loraB/lora.safetensors` — OPSD LoRA.

The Hugging Face model card and the demo script agree on mixing. With `--mix_steps=N`, the first `N` of the 8 distilled steps use `mix_alpha * DMD + (1 - mix_alpha) * OPSD`. The remaining steps use OPSD only. The published setting `--mix_steps=4 --mix_alpha=1.0` means the first four steps are DMD only and the last four are OPSD only. `--mix_steps` must be from 0 to 8, and `--mix_alpha` from 0 to 1. `--mix_steps` greater than 0 requires `--use_distill`.

When `--mix_steps` is greater than 0, the script loads `loraA` and `loraB` itself. The `--lora_path` value in the published command is the path used only when `--mix_steps` is 0, in which case that single file is merged statically. One sentence in the code README says the published mix settings "disable mixing and load the single LoRA." The script does the opposite whenever `--mix_steps` is greater than 0. Follow the script and the model card.

Other switches from the model card:

| Flag | Effect |
| --- | --- |
| `--use_int8` | Load `base_model_int8`, the weight-only INT8 DiT. The script describes this as reduced VRAM use. |
| `--use_fp8` | Calibrated Transformer Engine FP8 on selected FFN projections. Install Transformer Engine with PyTorch and CUDA support first. The script accepts this flag only for `--model_type avatar-v1.6`. |
| `--compile_dit` | Compile DiT blocks. The first runs pay a compile cost; later runs with the same shapes reuse the graphs. |
| `--compile_vae` | Compile the VAE encoder and decoder, with the same warm-up behavior. |

`--use_fp8` and `--use_int8` cannot be set together. The script rejects that combination. The model card says that if quality is the priority, reduce how many layers use FP8.

## More than two GPUs

For more than two GPUs, the model card says to export the All-to-All workspace and to set both process counts to the GPU count:

```shell
export LONGCAT_A2A_WORKSPACE=1
torchrun --nproc_per_node=4 run_demo_avatar_single_audio_to_video.py \
  --context_parallel_size=4 \
  --checkpoint_dir=./weights/LongCat-Video-Avatar-1.6 \
  --stage_1=ai2v \
  --input_json=inputs/spokesperson.json \
  --use_distill \
  --lora_path loraB/lora.safetensors \
  --model_type avatar-v1.6 \
  --mix_steps=4 \
  --mix_alpha=1.0
```

Use your real GPU count in both places.

## FlashAttention-3 on Hopper GPUs

On H20, H100, H200, and H800, the model card recommends FlashAttention-3. After it is installed, the model config should contain:

```json
{
  "enable_flashattn3": true,
  "enable_flashattn2": false
}
```

The code README points at `./weights/LongCat-Video/dit/config.json` for the foundation-model attention switch. Confirm the file you edit is the config the process actually loads before you rely on a speed change.

## Output and runtime facts from the script

* Launch with `torchrun`. The script reads `RANK` and initializes NCCL. A plain `python` launch fails that step.
* Default seed is 42 plus the process rank.
* Distilled 1.6 runs fix guidance at 1.0. The 1.5 tip about audio guidance between 3 and 5 applies to the non-distilled path; the distilled branch overwrites those scales.
* The vocal separator runs on the source audio. If it finds no vocal, the script raises `No vocal detected`.
* A NaN audio embedding raises `broken audio embedding with nan values`.
* Temporary vocal files are written under `./audio_temp_file`.
* Single-person outputs go to `./outputs_avatar_single` unless you pass `--output_dir`. Multi-person outputs go to `./outputs_avatar_multi`.

## Troubleshooting

| What you see | What it means |
| --- | --- |
| `No vocal detected` | The separator found no singing or speech. Use a recording with a clear voice. A music bed alone is not enough for this check. |
| `broken audio embedding with nan values` | The audio features failed. Try another take, with less clipping and a standard WAV or MP3, which are the formats in the published examples. |
| `Distillation LoRA not found` or `Mixing LoRA not found` | `--checkpoint_dir` should be `./weights/LongCat-Video-Avatar-1.6`, and `loraA/lora.safetensors` plus `loraB/lora.safetensors` should be inside it. |
| Missing tokenizer, text encoder, or VAE | Download `meituan-longcat/LongCat-Video` to `./weights/LongCat-Video`, as a sibling of the avatar folder. |
| `KeyError: 'RANK'` or an NCCL error at startup | Start with `torchrun`, and set `--nproc_per_node` to the number of GPUs you intend to use. `--context_parallel_size` should match. |
| CUDA out of memory | The authors do not publish a VRAM minimum. The script offers `--use_int8` for a smaller DiT, and `--resolution 480p` is the lower of the two published resolutions. More GPUs can share the work through `--context_parallel_size`. These switches are options the release documents; they are not a guarantee for a particular card. |
| The first compiled run is slow | Expected with `--compile_dit` or `--compile_vae`. Later runs of the same shapes reuse the compiled graphs. A new resolution can compile again. |
| `--use_fp8` fails immediately | Install Transformer Engine, and do not combine it with `--use_int8`. FP8 in this script is limited to `--model_type avatar-v1.6`. |
| `ffmpeg` or `ffprobe` not found | `conda install -c conda-forge ffmpeg`, then rerun with the environment activated. |
| The mouth never moves, or the clip is silent | Open the mp4 in a player that supports AAC audio. The script muxes the source file. Confirm `cond_audio.person1` points at that file and that the file has speech. |
| Two-person audio is a blur of both voices | `audio_type` `para` sums the tracks. Use `add` when the two people should speak one after the other. |
| You need a voice track you do not have yet | Create it in [VoiceStudio](https://github.com/Transformaiton/VoiceStudio) from a voice you are allowed to clone, export WAV, and pass that path as `person1`. |

Model questions for the authors go to longcat-team@meituan.com or to [their issue tracker](https://github.com/meituan-longcat/LongCat-Video/issues). Questions about this guide go to [info@transformaiton.com](mailto:info@transformaiton.com).
