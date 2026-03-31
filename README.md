## Causality-Aware Transformer Networks for Robotic Navigation (CAT)

This repository provides run instructions and supporting resources for **Causality-Aware Transformer (CAT) Networks** for visual robotic navigation using the AllenAct experimentation framework, as described in:

* Ruoyu Wang, Yao Liu, Yuanjiang Cao, Lina Yao. **Causality-Aware Transformer Networks for Robotic Navigation**. arXiv:2409.02669. ([PDF](https://arxiv.org/pdf/2409.02669), [arXiv](https://arxiv.org/abs/2409.02669))

CAT is an end-to-end framework for sequential navigation that is designed around a causal perspective on navigation: the direct determinants of the next state are largely driven by the current objective/state and the action taken at the current step. CAT introduces a **Causal Understanding Module** to encourage one-step direct causal relationships to stand out from other (weaker) associations.

Key ideas:

1. **End-to-End navigation model** trained with a unified objective (no task-specific inductive bias in the model architecture).
2. A **Causal Understanding Module** trained with an auxiliary **causal loss** that predicts the next visual-state representation from the current state representation and the current action representation.
3. In RL, CAT integrates the causal loss with PPO by optimizing:
   * `L_total = L_PPO - alpha * L_causal`

## Tasks and evaluation (paper)

The paper evaluates CAT on the following navigation tasks in the Reinforcement Learning setting:

1. **Object Navigation in RoboTHOR** (measured with Success Rate (SR) and Success weighted by Path Length (SPL)).
2. **Object Navigation in Habitat** (measured with SR, SPL, and Goal Distance (GD)).
3. **Point Navigation in Habitat** (measured with SR, SPL, and Goal Distance (GD)).

The paper also reports ablations and shows that the Causal Understanding Module improves performance in both RL and Supervised Learning settings.

## How this repo is organized

This project is built to run experiments using the **AllenAct** training/inference framework (the entry point is `main.py`, which delegates to `allenact.main`). The repository also includes:

* `docs/`: documentation and tutorials for running experiments with AllenAct.
* `pretrained_model_ckpts/`: helper scripts to download example navigation checkpoints for debugging/inference.
* `experiment_output/`: sample experiment logs (e.g., TensorBoard event files).

For the CAT paper method itself, see the arXiv paper above. For AllenAct experiment configuration patterns (defining models, tasks, and training pipelines), see `docs/`.

## Requirements

This codebase expects a PyTorch + AllenAct environment. Your exact runtime environment should match the dependencies described in:

* `requirements.txt`
* `docs/installation/installation-allenact.md`

## Installation

### 1. Install Python dependencies

From the repository root:

```bash
pip install -r requirements.txt
pip install -r dev_requirements.txt
```

### 2. Install AllenAct (+ plugins)

AllenAct supports multiple installation modes. A common choice is the standalone framework plus required plugins.

```bash
# Option A: minimal (framework only)
pip install allenact

# Option B: framework + plugins (simplest for many research workflows)
pip install allenact allenact_plugins[all]
```

See `docs/installation/installation-allenact.md` in this repository for alternative installation options and plugin-specific requirements.

## Download datasets and checkpoints

Datasets and environment assets are task/environment specific and are downloaded following the instructions in `docs/installation/download-datasets.md`.

For RoboTHOR navigation checkpoints (example weights for inference/debugging), you can run:

```bash
bash pretrained_model_ckpts/download_navigation_model_ckpts.sh robothor-pointnav-rgb-resnet
```

## Running training / evaluation

This repository uses AllenAct’s experiment configuration pattern. To run an experiment:

```bash
python main.py <PATH_TO_EXPERIMENT_CONFIG> -o <PATH_TO_OUTPUT> -c
```

Common options:

* `-o <PATH_TO_OUTPUT>`: directory where model checkpoints and logs are saved.
* `-c`: run “clean” configs/logging (exact semantics are defined by AllenAct).

For evaluation/inference with a checkpoint:

```bash
python main.py <PATH_TO_EXPERIMENT_CONFIG> -o <PATH_TO_OUTPUT> -c <PATH_TO_CHECKPOINT> --eval
```

In practice, the only part you need to customize for CAT reproduction is the experiment config Python file that instantiates:

* the CAT model (including the Causal Understanding Module and causal loss),
* the navigation task/environment,
* and the training pipeline (e.g., PPO with the causal loss weighted by `alpha`).

Use AllenAct’s experiment configuration tutorials in `docs/` as the template for how to structure that config.

## Citation

```bibtex
@inproceedings{wang2024causalityawaretransformer,
  title     = {Causality-Aware Transformer Networks for Robotic Navigation},
  author    = {Wang, Ruoyu and Liu, Yao and Cao, Yuanjiang and Yao, Lina},
  booktitle = {arXiv preprint arXiv:2409.02669},
  year      = {2024}
}
```

## License

MIT (see `LICENSE`).

## Acknowledgements

CAT is designed to align with the “end-to-end, no task-specific inductive bias” philosophy exemplified by Embodied AI navigation work such as EmbCLIP:
* [Embodied-clip repo](https://github.com/allenai/embodied-clip)
