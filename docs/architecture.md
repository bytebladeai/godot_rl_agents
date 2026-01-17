# Architecture - Godot RL Agents

## Executive Summary

Godot RL Agents is a Python library that bridges the Godot game engine with reinforcement learning frameworks. It enables game developers and AI researchers to train intelligent agents for games created in Godot using popular RL libraries.

**Version**: 0.8.1
**License**: MIT
**Author**: Edward Beeching

## Technology Stack

| Category | Technology | Version | Purpose |
|----------|------------|---------|---------|
| **Core Language** | Python | 3.8+ | Primary implementation language |
| **Package Manager** | pip/setuptools | - | Dependency management and distribution |
| **Default RL Framework** | StableBaselines3 | 2.0.0-2.4.0 | Primary training backend |
| **Deep Learning** | PyTorch | ≤2.8.0 | Neural network operations |
| **Environment Interface** | Gymnasium | ≤1.0.0 | Standard RL environment API |
| **Model Hub** | HuggingFace Hub | ≥0.10 | Model and environment sharing |
| **Model Export** | ONNX | ≤1.19.1 | Model inference format |
| **Testing** | pytest | ≥6.0 | Unit and integration testing |
| **CI/CD** | GitHub Actions | - | Automated testing pipeline |

### Optional Frameworks

| Framework | Extra | Platform Support |
|-----------|-------|------------------|
| Sample Factory | `[sf]` | Mac, Linux |
| Ray RLLib | `[rllib]` | Windows, Mac, Linux |
| CleanRL | `[cleanrl]` | Windows, Mac, Linux |

## Architecture Pattern

The library implements a **Layered Architecture** with the **Adapter Pattern** for framework integrations:

```
┌─────────────────────────────────────────────────────────────┐
│                    User Training Scripts                     │
│              (examples/*.py or custom scripts)               │
└─────────────────────────────┬───────────────────────────────┘
                              │
┌─────────────────────────────▼───────────────────────────────┐
│                     WRAPPER LAYER                            │
│  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐ ┌────────┐│
│  │    SB3      │ │   CleanRL   │ │   RLLib     │ │  SF    ││
│  │   Wrapper   │ │   Wrapper   │ │   Wrapper   │ │Wrapper ││
│  └──────┬──────┘ └──────┬──────┘ └──────┬──────┘ └───┬────┘│
└─────────┼───────────────┼───────────────┼────────────┼─────┘
          │               │               │            │
          └───────────────┴───────┬───────┴────────────┘
                                  │
┌─────────────────────────────────▼───────────────────────────┐
│                       CORE LAYER                             │
│                                                              │
│   ┌─────────────────────────────────────────────────────┐   │
│   │                    GodotEnv                          │   │
│   │  - Socket-based communication with Godot            │   │
│   │  - Observation/Action space management              │   │
│   │  - Multi-agent support                              │   │
│   │  - Step/Reset interface                             │   │
│   └─────────────────────────────────────────────────────┘   │
│                                                              │
│   ┌─────────────────┐  ┌─────────────────────────────────┐  │
│   │      utils      │  │       download_utils            │  │
│   │  - Helpers      │  │  - HuggingFace Hub integration  │  │
│   │  - Converters   │  │  - Example environment download │  │
│   └─────────────────┘  └─────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────┘
                              │
                              │ TCP Socket
                              │
┌─────────────────────────────▼───────────────────────────────┐
│                     GODOT ENGINE                             │
│                                                              │
│   ┌─────────────────────────────────────────────────────┐   │
│   │            Godot RL Agents Plugin                    │   │
│   │  - Sync node for Python communication               │   │
│   │  - AI sensors (raycast, grid, camera)               │   │
│   │  - AIController node                                │   │
│   │  - ONNX inference support                           │   │
│   └─────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
```

## Component Overview

### Core Components

#### 1. GodotEnv (`godot_rl/core/godot_env.py`)

The central class that implements the Gymnasium environment interface for Godot games.

**Responsibilities:**
- Establish TCP socket connection with Godot game
- Handle observation and action spaces (discrete, continuous, multi-dimensional)
- Manage step/reset cycle
- Support multiple parallel agents
- Handle seed management and episode termination

**Key Features:**
- Supports both exported games and in-editor training
- Configurable port and address
- Handles action repeat for slower physics updates

#### 2. Framework Wrappers (`godot_rl/wrappers/`)

Adapter classes that make GodotEnv compatible with different RL frameworks:

| Wrapper | File | Framework |
|---------|------|-----------|
| StableBaselinesGodotEnv | `stable_baselines_wrapper.py` | StableBaselines3 |
| CleanRLGodotEnv | `clean_rl_wrapper.py` | CleanRL |
| RayVectorGodotEnv | `ray_wrapper.py` | Ray RLLib |
| SampleFactoryGodotEnv | `sample_factory_wrapper.py` | Sample Factory |
| PettingZooWrapper | `petting_zoo_wrapper.py` | Multi-agent (PettingZoo API) |

#### 3. Download Utilities (`godot_rl/download_utils/`)

Helper functions for asset management:

| Module | Purpose |
|--------|---------|
| `from_hub.py` | Download environments from HuggingFace Hub |
| `download_examples.py` | Download training examples |
| `download_godot_editor.py` | Download Godot editor binaries |

#### 4. CLI Interface (`godot_rl/main.py`)

Command-line interface providing the `gdrl` command for common operations.

### ONNX Export (`godot_rl/wrappers/onnx/`)

Utilities for exporting trained models to ONNX format for inference within Godot games.

## Data Flow

### Training Flow

```
1. User starts training script (e.g., stable_baselines3_example.py)
2. Script creates GodotEnv with wrapper for chosen framework
3. GodotEnv establishes TCP connection to Godot game
4. Training loop:
   a. Framework calls env.step(action)
   b. GodotEnv sends action over socket to Godot
   c. Godot executes physics step, collects observations
   d. Godot sends (obs, reward, done, info) back over socket
   e. GodotEnv returns data to framework
   f. Framework updates policy
5. Training completes, model saved
```

### Inference Flow (with ONNX)

```
1. Export trained model to ONNX format
2. Load ONNX model in Godot (via .NET/C# support)
3. Godot game runs inference locally without Python
```

## Entry Points

### CLI Commands

| Command | Module | Description |
|---------|--------|-------------|
| `gdrl` | `godot_rl.main:main` | Main CLI |
| `gdrl.interactive` | `godot_rl.core.godot_env:interactive` | Interactive testing mode |
| `gdrl.download_editor` | `godot_rl.download_utils.download_godot_editor:download_editor` | Download Godot |
| `gdrl.env_from_hub` | `godot_rl.download_utils.from_hub:main` | Download environment |

### Python API

```python
# Core environment
from godot_rl.core.godot_env import GodotEnv

# Framework-specific wrappers
from godot_rl.wrappers.stable_baselines_wrapper import StableBaselinesGodotEnv
from godot_rl.wrappers.clean_rl_wrapper import CleanRLGodotEnv
from godot_rl.wrappers.ray_wrapper import RayVectorGodotEnv
from godot_rl.wrappers.sample_factory_wrapper import SampleFactoryGodotEnv

# ONNX export
from godot_rl.wrappers.onnx.stable_baselines_export import export_onnx
```

## Testing Strategy

### Test Categories

| Category | Location | Coverage |
|----------|----------|----------|
| Unit Tests | `tests/test_*.py` | Core functionality |
| Integration Tests | `tests/test_*_training.py` | Framework integrations |
| Export Tests | `tests/test_sb3_onnx_export.py` | ONNX export |
| Performance | `tests/benchmark_env.py` | Environment performance |

### CI Matrix

- **Python Versions**: 3.8, 3.9, 3.10
- **Platforms**: Ubuntu, Windows
- **Framework Variants**: SB3, SB3+SF, RLLib

## Key Design Decisions

### 1. Socket-based Communication

**Decision**: Use TCP sockets for Python-Godot communication
**Rationale**:
- Cross-platform compatibility
- Supports both local and remote training
- Minimal dependencies on Godot side

### 2. Wrapper Pattern for Frameworks

**Decision**: Create separate wrapper classes per framework
**Rationale**:
- Each framework has unique API requirements
- Allows framework-specific optimizations
- Clean separation of concerns

### 3. Gymnasium as Base Interface

**Decision**: Base GodotEnv on Gymnasium (OpenAI Gym successor)
**Rationale**:
- Industry standard RL environment interface
- Wide framework compatibility
- Active maintenance and community

### 4. ONNX for Inference

**Decision**: Export to ONNX for production inference
**Rationale**:
- No Python dependency in deployed games
- Broad runtime support
- Model portability

## Integration Points

### External Dependencies

| System | Integration Method | Purpose |
|--------|-------------------|---------|
| Godot Engine | TCP Socket | Game environment |
| HuggingFace Hub | REST API | Model/env sharing |
| PyTorch | Python import | Training backend |
| TensorBoard | File logging | Training visualization |

### Plugin Dependency

The library requires the [Godot RL Agents Plugin](https://github.com/edbeeching/godot_rl_agents_plugin) to be installed in Godot projects for:
- Sync node (Python communication)
- AIController node (agent setup)
- AI sensors (observation collection)
- ONNX inference runtime
