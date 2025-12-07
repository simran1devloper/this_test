# Interrupt Manager AI Agent Workflow

![Flowchart](/home/sonia/.gemini/antigravity/brain/85f7df8a-6c3c-4754-9833-3d6f65982f32/interrupt_manager_flowchart_v2_1765096776442.png)

This workflow describes how to set up, configure, and verify the Interruption Manager library.

## 1. Environment Setup

The project requires Python 3.9+ and several dependencies.

### Installation
```bash
pip install -r requirements.txt
# Additional system dependencies
pip install nltk
```

> [!NOTE]
> If `nltk` is missing, the provided `demo.py` includes a mock implementation for testing purposes.

## 2. Configuration

Configuration is handled via `.env` or `config.py`.

### Key Environment Variables
| Variable | Description | Default |
| :--- | :--- | :--- |
| `INTERRUPT_MODE` | Logic Engine: `RULES`, `ML`, `LLM`, `HYBRID` | `RULES` |
| `LK_IGNORE_WORDS` | Comma-separated words to ignore | `yeah,ok,hmm` |
| `LK_INTERRUPT_WORDS` | Comma-separated words that trigger interrupt | `stop,wait,no` |
| `LK_LATENCY_BUDGET` | Latency threshold | `0.2` |

## 3. Running the Verification Demo

A `demo.py` script is provided to simulate the agent's behavior without a full LiveKit connection.

### Usage
Run the demo as a module to ensure imports work correctly:

```bash
# Ensure you are in the parent directory of 'tool' or set PYTHONPATH
export PYTHONPATH=$PYTHONPATH:$(dirname $(pwd))
python3 -m tool.demo
```

### Expected Output
The demo tests several phrases against the `RULES` engine:
- "yeah" -> `IGNORE` (Backchannel)
- "stop" -> `INTERRUPT` (Command)
- "I like apples" -> `NORMAL` (User Input)

## 4. Integration Guide

To use this in your own agent:

1.  **Initialize**:
    ```python
    from tool.interruption_manager import InterruptionManager
    manager = InterruptionManager()
    ```

2.  **Analyze**:
    Call `analyze()` on every transcript chunk.
    ```python
    decision = await manager.analyze(transcript, is_speaking=True, audio_meta={...})
    if decision == "INTERRUPT":
        await stop_tts()
    ```

## 5. Troubleshooting

- **Import Errors**: Ensure the directory is treated as a package (presence of `__init__.py`) and `PYTHONPATH` includes the parent directory.
- **Rules Mode**: Ensure `nltk` is installed for the real `RulesEngine` to work optimally.
- **LLM Mode**: Requires valid API keys for OpenAI/Ollama/Gemini in `.env`.
