# ML4SCI – QMLHEP GSoC 2026
## Quantum Circuit Design with LLMs — Evaluation Submission

**Author:** Jen-Yu Chang (Leo) · leo07010@gmail.com  
Visiting Researcher, The Matter Lab, University of Toronto (Prof. Alán Aspuru-Guzik)  
M.S. Electrophysics, NYCU Taiwan  
Starting Sept 2026: Cambridge Milner Therapeutics Institute

---

## Overview

This submission implements a **multi-tool agentic closed-loop framework** for
variational quantum circuit (VQC) design, addressing all four expected outcomes
of the ML4SCI QMLHEP GSoC 2026 project.

### Tool Architecture

| # | Tool | Responsibility |
|---|---|---|
| 1 | `hilbert_dim` | Qubit count / simulation feasibility check |
| 2 | `build_circuit` | Design & register a parameterised VQC ansatz |
| 3 | `measure_circuit_quality` | Expressibility (KL from Haar), entanglement (Meyer–Wallach), depth |
| 4 | `train_qnn` | Train hybrid classical–quantum model; return accuracy |
| 5 | `compare_ansatze` | Batch-evaluate & rank multiple circuits |
| 6 | `suggest_circuit_improvement` | Diagnose failure modes; propose fixes |
| 7 | `run_noise_simulation` | Depolarising noise sweep for NISQ viability |

### Closed-Loop Design Cycle

```
Agent designs circuit spec
        ↓
  build_circuit → measure_circuit_quality
        ↓ (quality feedback)
  train_qnn → accuracy
        ↓ (performance feedback)
  suggest_circuit_improvement
        ↓ (architectural fix)
  run_noise_simulation → NISQ viability
        ↓
  Final report & recommendation
```

### Task Coverage

| Task | Description |
|---|---|
| **Task 1** | Hello World: `hilbert_dim` tool + agent demo |
| **Task 2** | Agent-controlled QNN training via `build_circuit` + `train_qnn` |
| **Task 3** | Closed-loop hyperparameter optimisation (≤6 trials, autonomous LR search) |
| **Full Loop** | Complete design cycle with all 7 tools, autonomous design → diagnose → improve → noise-test |

---

## Setup

### Requirements
- Python 3.13+
- Anthropic API key (`sk-ant-...`)

### Installation

```bash
pip install orchestral-ai pennylane torch torchvision numpy matplotlib
```

### Running

1. Clone this repository  
2. Open `ML4SCI_QMLHEP_GSoC2026_LeoChang.ipynb` in Jupyter  
3. Set your API key in **Cell 1**:
   ```python
   os.environ.setdefault("ANTHROPIC_API_KEY", "sk-ant-YOUR-KEY-HERE")
   ```
4. Run all cells top-to-bottom  

> **Note:** Cell 11 (verification) runs without an API key. All agent cells (12–15) require a valid key.

### Dataset
MNIST is downloaded automatically. If unavailable (e.g., network-restricted environment),
a synthetic substitute of identical shape and label structure is generated automatically.

---

## Project Structure

```
gsoc2026_qml_leo/
├── ML4SCI_QMLHEP_GSoC2026_LeoChang.ipynb   # Main submission notebook
├── README.md                                 # This file
├── requirements.txt                          # Python dependencies
└── .gitignore
```

---

## Key Technical Details

**QNN Architecture:**
```
Input image (1×28×28)
└─ Classical Encoder: Linear(784→64) ReLU Linear(64→4) Tanh
       └─ Quantum Layer: 4-qubit PennyLane VQC (AngleEmbedding + variational layers)
              └─ Classical Head: Linear(4→10) → class logits
```

**Circuit Quality Metrics:**
- **Expressibility**: KL divergence from Haar-random fidelity distribution  
  (Sim et al., *Advanced Quantum Technologies*, 2019)
- **Entanglement**: Meyer-Wallach score (average linear entropy of single-qubit RDMs)
- **Gate metrics**: depth, two-qubit gate count, parameter count

**Agent Framework:** [Orchestral AI](https://github.com/orchestralAI/orchestral-ai)  
**LLM:** Claude 3.5 Haiku (Anthropic)  
**Quantum Backend:** PennyLane `default.qubit`  

---

## Relation to Prior Work

This project directly extends the author's research at The Matter Lab (Aspuru-Guzik group):
the agentic tool-calling loop mirrors the GRPO reward loop used in **TileGQE-MTS**
(paper in preparation for *PRX Quantum*), where a GPT-2-scale Transformer generates
quantum circuits guided by a reward signal — but here the LLM itself plays the role of
the optimizer, interacting with the quantum simulator through structured tools rather
than token sequences.

---

## Contact

leo07010@gmail.com  
Questions about the ML4SCI project: ml4-sci@cern.ch
