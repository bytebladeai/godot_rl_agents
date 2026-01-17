# Source Tree Analysis - Godot RL Agents

## Project Structure Overview

This document provides an annotated directory tree for the Godot RL Agents library, highlighting critical directories, entry points, and key files.

## Complete Directory Tree

```
godot_rl_agents/
├── __init__.py                    # Package root marker
├── LICENSE                        # MIT license
├── README.md                      # Main documentation and quickstart guide
├── pyproject.toml                 # Modern Python packaging configuration
├── setup.cfg                      # Package metadata, dependencies, and entry points
├── Makefile                       # Build automation (style, quality, test, wheel)
├── ppo_test.yaml                  # PPO test configuration
├── BallChase.zip                  # Example environment archive
│
├── godot_rl/                      # [CORE LIBRARY] Main Python package
│   ├── __init__.py               # Package initialization
│   ├── main.py                   # [ENTRY POINT] CLI entrypoint (gdrl command)
│   │
│   ├── core/                     # [CORE] Environment interface
│   │   ├── __init__.py
│   │   ├── godot_env.py         # [KEY] GodotEnv - core environment class
│   │   └── utils.py             # Utility functions
│   │
│   ├── download_utils/           # [UTILITIES] Download helpers
│   │   ├── __init__.py
│   │   ├── download_examples.py  # Download example environments
│   │   ├── download_godot_editor.py  # Download Godot editor
│   │   └── from_hub.py          # [KEY] HuggingFace Hub integration
│   │
│   └── wrappers/                 # [WRAPPERS] RL framework integrations
│       ├── __init__.py
│       ├── stable_baselines_wrapper.py  # [KEY] StableBaselines3 wrapper
│       ├── clean_rl_wrapper.py          # [KEY] CleanRL wrapper
│       ├── ray_wrapper.py               # [KEY] Ray RLLib wrapper
│       ├── sample_factory_wrapper.py    # [KEY] Sample Factory wrapper
│       ├── petting_zoo_wrapper.py       # Multi-agent PettingZoo wrapper
│       ├── sbg_single_obs_wrapper.py    # Single observation wrapper
│       └── onnx/                 # ONNX export utilities
│           ├── __init__.py
│           └── stable_baselines_export.py  # SB3 ONNX export
│
├── examples/                      # [EXAMPLES] Training script examples
│   ├── stable_baselines3_example.py     # [START HERE] SB3 training script
│   ├── clean_rl_example.py              # CleanRL training script
│   ├── clean_rl_pqn_example.py          # CleanRL PQN example
│   ├── rllib_example.py                 # Ray RLLib training script
│   ├── rllib_config.yaml                # RLLib configuration
│   ├── sample_factory_example.py        # Sample Factory training script
│   ├── stable_baselines3_hp_tuning.py   # Hyperparameter tuning example
│   ├── sb3_imitation.py                 # Imitation learning example
│   └── clean_rl_gru_ppo/                # GRU-based PPO example
│       ├── clean_rl_ppo_gru_discrete_actions_example.py
│       └── readme.md
│
├── tests/                         # [TESTS] Test suite
│   ├── test_godot_env.py         # Core environment tests
│   ├── test_sb3_training.py      # SB3 training tests
│   ├── test_rllib.py             # RLLib integration tests
│   ├── test_sample_factory.py    # Sample Factory tests
│   ├── test_sb3_onnx_export.py   # ONNX export tests
│   ├── test_call_method.py       # Method call tests
│   ├── test_action_space_preprocessor.py  # Action space tests
│   └── benchmark_env.py          # Performance benchmarking
│
├── docs/                          # [DOCUMENTATION] User guides and references
│   ├── CUSTOM_ENV.md             # Custom environment creation guide
│   ├── ADV_STABLE_BASELINES_3.md # Advanced SB3 usage
│   ├── ADV_CLEAN_RL.md           # Advanced CleanRL usage
│   ├── ADV_RLLIB.md              # Advanced RLLib usage
│   ├── ADV_SAMPLE_FACTORY.md     # Advanced Sample Factory usage
│   ├── EXAMPLE_ENVIRONMENTS.md   # Example environments reference
│   ├── GENERAL_TIPS.md           # General tips and best practices
│   ├── IMITATION_LEARNING.md     # Imitation learning guide
│   ├── NODE_REFERENCE.md         # Godot node reference
│   ├── TRAINING_MULTIPLE_POLICIES.md  # Multi-policy training
│   ├── TRAINING_STATISTICS.md    # Training statistics docs
│   ├── TROUBLESHOOTING.md        # Troubleshooting guide
│   ├── WORKING_WITH_CSHARP.md    # C# integration guide
│   ├── ROADMAP.md                # Project roadmap
│   └── *.png                     # Documentation images
│
├── scripts/                       # [SCRIPTS] Automation scripts
│   └── get_all_examples_from_hub.sh  # Download examples from HF Hub
│
├── godot_rl_agents_plugin/        # [PLUGIN] Godot plugin (separate repo integration)
│   └── (empty - linked via git submodule)
│
└── .github/                       # [CI/CD] GitHub configuration
    └── workflows/
        ├── quality.yml           # Code quality checks
        └── test-ci.yml           # Multi-platform test matrix
```

## Critical Folders Summary

| Folder | Purpose | Key Files |
|--------|---------|-----------|
| `godot_rl/core/` | Core environment interface | `godot_env.py` - Main GodotEnv class |
| `godot_rl/wrappers/` | RL framework integrations | Framework-specific wrapper classes |
| `godot_rl/download_utils/` | Asset downloading | HuggingFace Hub integration |
| `examples/` | Training scripts | Ready-to-use training examples |
| `tests/` | Test suite | pytest-based tests |
| `docs/` | Documentation | Guides for each RL framework |

## Entry Points

### CLI Entry Points (from setup.cfg)

| Command | Module | Description |
|---------|--------|-------------|
| `gdrl` | `godot_rl.main:main` | Main CLI interface |
| `gdrl.interactive` | `godot_rl.core.godot_env:interactive` | Interactive environment mode |
| `gdrl.download_editor` | `godot_rl.download_utils.download_godot_editor:download_editor` | Download Godot editor |
| `gdrl.env_from_hub` | `godot_rl.download_utils.from_hub:main` | Download environment from Hub |

### Package Import Entry Point

```python
from godot_rl.core.godot_env import GodotEnv
```

## Architecture Pattern

This library follows a **Wrapper/Adapter Pattern** architecture:

1. **Core Layer** (`godot_rl/core/`): Provides the base `GodotEnv` class that interfaces with Godot Engine via socket communication
2. **Wrapper Layer** (`godot_rl/wrappers/`): Adapts the core environment to different RL framework APIs
3. **Utility Layer** (`godot_rl/download_utils/`): Provides helper functions for asset management

## Data Flow

```
┌─────────────────┐    socket    ┌─────────────────┐
│   Godot Game    │◄────────────►│    GodotEnv     │
│   (Godot RL     │              │   (core layer)  │
│    Plugin)      │              └────────┬────────┘
└─────────────────┘                       │
                                          │ wraps
                                          ▼
                              ┌───────────────────────┐
                              │   Framework Wrapper   │
                              │  (SB3/CleanRL/RLLib)  │
                              └───────────┬───────────┘
                                          │
                                          ▼
                              ┌───────────────────────┐
                              │   RL Training Loop    │
                              │   (user's script)     │
                              └───────────────────────┘
```
