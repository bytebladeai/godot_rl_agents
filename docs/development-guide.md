# Development Guide - Godot RL Agents

## Prerequisites

- **Python**: 3.8 or higher (tested with 3.8, 3.9, 3.10)
- **pip**: Latest version recommended
- **git-lfs**: Required for downloading example environments
- **Virtual Environment**: Strongly recommended (venv or Conda)

## Installation

### User Installation (from PyPI)

```bash
pip install godot-rl
```

### Developer Installation (from source)

```bash
# Clone the repository
git clone git@github.com:YOUR_USERNAME/godot_rl_agents.git
cd godot_rl_agents

# Create and activate virtual environment
python -m venv venv
source venv/bin/activate  # Linux/Mac
# or: venv\Scripts\activate  # Windows

# Install in editable mode with dev dependencies
pip install -e ".[dev]"
```

### Optional Dependencies

Install additional RL framework support as needed:

```bash
# Sample Factory support (Mac, Linux only)
pip install -e ".[sf]"

# Ray RLLib support
pip install -e ".[rllib]"

# CleanRL support (with Weights & Biases)
pip install -e ".[cleanrl]"
```

## Development Commands

The project uses a Makefile for common tasks:

| Command | Description |
|---------|-------------|
| `make style` | Format code with black and isort |
| `make quality` | Run linting checks (black, isort, flake8) |
| `make test` | Run pytest test suite |
| `make download_examples` | Download example environments from HuggingFace Hub |
| `make wheel` | Build distribution packages |

## Code Style

- **Formatter**: black (line length: 120)
- **Import sorting**: isort (line width: 120)
- **Linter**: flake8 (max line length: 120)
- **Target Python version**: 3.10

### Format your code before committing

```bash
make style
```

### Check code quality

```bash
make quality
```

## Testing

### Setup for Testing

Tests require example environments to be downloaded:

```bash
# Install git-lfs first if not already installed
# Then download examples
make download_examples
```

Note: Examples are only available for Linux and Windows.

### Run Tests

```bash
make test
# or
python -m pytest tests/
```

### Test Files

| Test File | Description |
|-----------|-------------|
| `test_godot_env.py` | Core environment tests |
| `test_sb3_training.py` | StableBaselines3 training tests |
| `test_rllib.py` | Ray RLLib integration tests |
| `test_sample_factory.py` | Sample Factory tests |
| `test_sb3_onnx_export.py` | ONNX export tests |
| `test_call_method.py` | Method invocation tests |
| `test_action_space_preprocessor.py` | Action space handling tests |
| `benchmark_env.py` | Performance benchmarking utility |

## CI/CD Pipeline

The project uses GitHub Actions for continuous integration:

### Quality Workflow (`.github/workflows/quality.yml`)

- Triggers: Push/PR to main branch
- Runs on: ubuntu-latest, Python 3.10.10
- Tasks: Code quality checks (`make quality`)

### Test Workflow (`.github/workflows/test-ci.yml`)

- Triggers: Push/PR to main branch
- Matrix testing:
  - Python versions: 3.8, 3.9, 3.10.10
  - Operating systems: ubuntu-latest, windows-latest
- Test configurations:
  - Standard tests (SB3, Sample Factory)
  - RLLib-specific tests (separate job)

## Contributing

1. Fork the repository
2. Clone your fork
3. Create a virtual environment and install dev dependencies
4. Make your changes
5. Format code: `make style`
6. Check quality: `make quality`
7. Run tests: `make test`
8. Submit a Pull Request to the main branch

### Types of Contributions Welcome

- New environments made in Godot
- Improvements to documentation
- Additions to the Python codebase
- Bug fixes and issue resolutions

## Building and Publishing

### Build Distribution

```bash
make wheel
```

This will:
1. Clean the `dist/` directory
2. Build source and wheel distributions
3. Upload to PyPI via twine

## Environment Variables

No specific environment variables are required for basic usage. However:

- Training scripts may use `.env` files for configuration
- ONNX export requires the Godot editor with the .NET version

## Project Structure for Development

Key directories for developers:

- `godot_rl/core/` - Core environment implementation
- `godot_rl/wrappers/` - RL framework adapters
- `tests/` - Test suite
- `examples/` - Training script templates
