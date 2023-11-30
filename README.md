# 4D-fy - threestudio

| [Project Page](https://sherwinbahmani.github.io/4dfy/) | [Paper](https://sherwinbahmani.github.io/4dfy/paper.pdf) |

- **This code is forked from [threestudio](https://github.com/threestudio-project/threestudio).**

## Installation

### Install threestudio

**This part is the same as original threestudio. Skip it if you already have installed the environment.**

See [installation.md](docs/installation.md) for additional information, including installation via Docker.

- You must have an NVIDIA graphics card with at least 40GB VRAM and have [CUDA](https://developer.nvidia.com/cuda-downloads) installed.
- Install `Python >= 3.8`.
- (Optional, Recommended) Create a virtual environment:

```sh
python3 -m virtualenv venv
. venv/bin/activate

# Newer pip versions, e.g. pip-23.x, can be much faster than old versions, e.g. pip-20.x.
# For instance, it caches the wheels of git packages to avoid unnecessarily rebuilding them later.
python3 -m pip install --upgrade pip
```

- Install `PyTorch >= 1.12`. We have tested on `torch1.12.1+cu113` and `torch2.0.0+cu118`, but other versions should also work fine.

```sh
# torch1.12.1+cu113
pip install torch==1.12.1+cu113 torchvision==0.13.1+cu113 --extra-index-url https://download.pytorch.org/whl/cu113
# or torch2.0.0+cu118
pip install torch torchvision --index-url https://download.pytorch.org/whl/cu118
```

- (Optional, Recommended) Install ninja to speed up the compilation of CUDA extensions:

```sh
pip install ninja
```

- Install dependencies:

```sh
pip install -r requirements.txt
```

### Install MVDream
MVDream multi-view diffusion model is provided in a different codebase. Install it by:

```sh
git clone https://github.com/bytedance/MVDream extern/MVDream
pip install -e extern/MVDream 
```

## Quickstart

Our model is trained in 3 stages and there are three different config files for every stage. Training has to be resumed after finishing a stage.

```sh
seed=0
gpu=0

# Stage 1
# python launch.py --config configs/fourdfy_stage_1.yaml --train --gpu $gpu exp_root_dir=$exp_root_dir seed=$seed system.prompt_processor.prompt="a dog riding a skateboard"

# ~~Stage 2~~ (Drop VSD stage)
# ckpt=/path/to/fourdfy_stage_1/a_dog_riding_a_skateboard@timestamp/ckpts/last.ckpt
# python launch.py --config configs/fourdfy_stage_2.yaml --train --gpu $gpu exp_root_dir=$exp_root_dir seed=$seed system.prompt_processor.prompt="a dog riding a skateboard" system.weights=$ckpt

# Stage 3
# ckpt=/path/to/fourdfy_stage_2/a_dog_riding_a_skateboard@timestamp/ckpts/last.ckpt
# python launch.py --config configs/fourdfy_stage_3.yaml --train --gpu $gpu exp_root_dir=$exp_root_dir seed=$seed system.prompt_processor.prompt="a dog riding a skateboard" system.weights=$ckpt
```

## Tips
- **Memory Usage**. Depending on the text prompt, stage 3 might not fit on a 40/48 GB GPU. An easy way to reduce memory usage is to reduce the number of ray samples with system.renderer.num_samples_per_ray=256 or system.renderer.num_samples_per_ray=128. Another way is to reduce the rendering resolution for the video model with data.single_view.width_vid=144 and data.single_view.height_vid=80. **Using modified parameters just fits on a 3090 for dog on skateboard demo**
- **More motion**. To increase the motion, the learning rate for the video model can be increased to system.loss.lambda_sds_video=0.3 or system.loss.lambda_sds_video=0.5.


## Credits

This code is built on the [threestudio-project](https://github.com/threestudio-project/threestudio) and [MVDream-threestudio](https://github.com/bytedance/MVDream-threestudio). Thanks to the maintainers for their contribution to the community!

## Citing

If you find 4D-fy helpful, please consider citing:

```
@article{bah20234dfy,
  author = {Bahmani, Sherwin and Skorokhodov, Ivan and Rong, Victor and Wetzstein, Gordon and Guibas, Leonidas and Wonka, Peter and Tulyakov, Sergey and Park, Jeong Joon and Tagliasacchi, Andrea and Lindell, David B.},
  title = {4D-fy: Text-to-4D Generation Using Hybrid Score Distillation Sampling},
  journal = {arXiv},
  year = {2023},
}
```
