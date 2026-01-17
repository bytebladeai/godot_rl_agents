# Project Overview - Godot RL Agents

## What is Godot RL Agents?

Godot RL Agents is a fully open-source Python package that enables video game creators, AI researchers, and hobbyists to train intelligent behaviors for Non-Player Characters (NPCs) using Deep Reinforcement Learning.

## Key Features

- **Godot Engine Integration**: Seamless interface between games created in Godot and ML algorithms running in Python
- **Multi-Framework Support**: Wrappers for 4 major RL frameworks:
  - StableBaselines3 (Windows, Mac, Linux)
  - Sample Factory (Mac, Linux)
  - Ray RLLib (Windows, Mac, Linux)
  - CleanRL (Windows, Mac, Linux)
- **Memory-Based Agents**: Support for LSTM and attention-based architectures
- **2D and 3D Support**: Works with both 2D and 3D game environments
- **AI Sensors**: Suite of sensors to augment agent observation capabilities
- **ONNX Export**: Export trained models for inference directly in Godot
- **HuggingFace Integration**: Share and download environments and models

## Quick Reference

| Property | Value |
|----------|-------|
| **Package Name** | `godot-rl` |
| **Version** | 0.8.1 |
| **License** | MIT |
| **Primary Language** | Python 3.8+ |
| **Repository Type** | Monolith (single package) |
| **Project Type** | Python Library |
| **Author** | Edward Beeching |

## Technology Stack Summary

| Component | Technology |
|-----------|------------|
| Core | Python, PyTorch |
| Default RL | StableBaselines3 |
| Environment API | Gymnasium |
| Model Export | ONNX |
| Model Hub | HuggingFace Hub |
| Testing | pytest |
| CI/CD | GitHub Actions |

## Architecture Overview

The library follows a **Wrapper/Adapter Pattern**:

1. **Core Layer**: `GodotEnv` class provides socket-based communication with Godot Engine
2. **Wrapper Layer**: Framework-specific adapters (SB3, CleanRL, RLLib, SF)
3. **Utility Layer**: Download helpers, ONNX export tools

## Getting Started

### Installation

```bash
pip install godot-rl
```

### Download Example Environment

```bash
gdrl.env_from_hub -r edbeeching/godot_rl_JumperHard
```

### Train an Agent

```bash
python examples/stable_baselines3_example.py \
  --env_path=examples/godot_rl_JumperHard/bin/JumperHard.x86_64 \
  --experiment_name=Experiment_01 \
  --viz
```

## Documentation Links

- [Architecture](./architecture.md) - Detailed system architecture
- [Source Tree Analysis](./source-tree-analysis.md) - Annotated directory structure
- [Development Guide](./development-guide.md) - Setup and contribution guide
- [Custom Environment Tutorial](./CUSTOM_ENV.md) - Create your own environments
- [Framework Guides](./ADV_STABLE_BASELINES_3.md) - Advanced usage per framework

## External Resources

- [GitHub Repository](https://github.com/edbeeching/godot_rl_agents)
- [Discord Community](https://discord.gg/HMMD2J8SxY)
- [AAAI-2022 Paper](https://arxiv.org/abs/2112.03636)
- [Video Tutorial](https://www.youtube.com/watch?v=f8arMv_rtUU)
- [Example Environments](https://github.com/edbeeching/godot_rl_agents_examples)
- [Godot Plugin](https://github.com/edbeeching/godot_rl_agents_plugin)

## Use Cases

1. **Game AI Development**: Train NPCs with complex behaviors
2. **RL Research**: Benchmark algorithms in game environments
3. **Automated Testing**: Use RL agents for gameplay testing
4. **Education**: Learn reinforcement learning with visual environments

## Why Godot RL Agents?

- **Free and Open Source**: MIT license with no royalties or restrictions
- **Hackable**: Compact, concise codebase with minimal abstraction
- **Cross-Platform**: Supports Windows, Mac, and Linux
- **Active Community**: Discord support and regular updates
