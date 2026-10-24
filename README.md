[README.md](https://github.com/user-attachments/files/33908191/README.md)
# 5G Network Slicing: DP, RL, and SLA-constrained experiments

Research code and results associated with the manuscript **Admission Control and Pricing in 5G Network Slicing: A Markov Decision Process Approach with Reinforcement Learning Benchmarks** (JNCA submission).

> **Reproducibility status:** This repository consolidates four previously supplied experimental archives and adds an Experiment 5 ablation implementation and its archived results. It is not claimed that every paper table/figure has been freshly reproduced on this machine. Some result files are archived from earlier runs. Runtime, hardware, and training budgets affect reproducibility. See `REPRODUCIBILITY_STATUS.md`.

## Setup

```bash
python -m venv .venv
# Windows: .venv\Scripts\activate
# Linux/macOS: source .venv/bin/activate
python -m pip install -r requirements.txt
python verify_repository.py
```

## Layout

| Folder | Content |
|---|---|
| `experiments/experiment_1_2_capacity/` | Experiments 1--2 and additional capacity-stress comparisons (Section 6.4) |
| `experiments/experiment_3_dp_rl/` | Exact DP versus Double Q-learning, DQN, PPO |
| `experiments/experiment_4_sla/` | Anchored PPO and SLA validation, including archived checkpoints |
| `experiments/experiment_4_cmdp_comparison/` | Exact CMDP LP and constrained-PPO comparison, separate historical package |
| `experiments/experiment_5_ablation/` | Congestion-penalty ablation, common-reward evaluation |
| `experiments/experiment_5_stress/` | Additional congestion-penalty stress tests |

The two Experiment 4 folders intentionally remain separate: they are distinct historical variants, **not interchangeable final implementations**. The Experiment 5 scripts were integrated from previously produced research archives, with the ablation script's absolute local paths fixed.

## Run individual experiments

From the repository root:

```bash
cd experiments/experiment_1_2_capacity
python run_original_experiments.py --experiment 1 --output reproduced/experiment_1
python run_original_experiments.py --experiment 2 --output reproduced/experiment_2
```

```bash
cd experiments/experiment_3_dp_rl
python run_reproduce.py --help
python verify.py
```

```bash
cd experiments/experiment_4_sla
python scripts/solve_cmdp.py --help
python scripts/train.py --help
python scripts/validate.py --help
```

```bash
cd experiments/experiment_5_ablation
python common_evaluation.py
```

```bash
cd experiments/experiment_5_stress
python stress_test.py
```

**Warning:** Exact DP and RL runs can be computationally expensive. Inspect the individual README and script defaults before running. Several scripts use fixed relative paths and are designed to run from their own experiment folder. The commands above are entry points, not a guarantee of full-scale reproduction in a short time.

## Core model

The state is `(nH, nL)` with `2*nH + nL <= C`. Prices affect class-specific Poisson arrival rates. Admission quotas restrict admitted arrivals, and binomial departures release PRBs. The baseline uses `lambda0H=5`, `lambda0L=7`, `betaH=.60`, `betaL=.70`, `muH=.25`, `muL=.20`, `gamma=.99`, `kappa=.15`, `xiH=.25`, `xiL=.15`; price menus are `PH={1,2,3}`, `PL={.75,1.5,2.25}`, and baseline quota menus are `0..2` per class. Capacity stress uses quotas `0..4`.

## Interpretation

DP optimizes **discounted** expected reward, while simulation tables often report **average realized reward per slot**. Differences in average reward should not be described as exact discounted optimality gaps. Experiment 5 compares policies under a **common kappa=.15 reward definition**; comparing each policy under its own reward would be invalid for a monotonicity claim. See the notes in each experiment folder.

## Publication and attribution

The repository is provided as research software, with no operational 5G traffic data. Please check the author/copyright and third-party dependency license information before publishing. Cite the manuscript when available.
