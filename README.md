# Auto Code

Auto Code is a local agentic development environment (ADE) for Apple Silicon Macs. Chat with a model, or ask the agent to write a JavaScript utility and run its tests. The conversation shows the elapsed time, test results, and files saved to your project.

## Download and run

1. Download **Auto-Code-0.1.0-arm64.dmg** from the [first release](https://github.com/ANAYSSA/auto-code/releases/tag/v0.1.0).
2. Open the disk image and drag **Auto Code** into **Applications**.
3. Open **Models** and select **Best for my Mac** to find coding models that fit the detected chip and memory. Download a model once. Inference runs offline.
4. On **Home**, open a project folder with **+**, choose **Agent**, and describe a JavaScript function and its expected behavior.

Requires macOS 14 or later on Apple Silicon. The app interface is in Russian. This first release is ad-hoc signed and has not been notarized by Apple.

## Available models

| Model | Download | Suggested use |
| --- | --- | --- |
| Qwen2.5-Coder 1.5B Instruct | 1.12 GB | Short code explanations and quick questions |
| Qwen2.5-Coder 7B Instruct | 4.68 GB | Writing, explaining, and fixing code |
| Gemma 4 12B IT | 6.98 GB | General programming discussions and explanations |

The disk image includes llama.cpp. Model weights are downloaded separately. The **Settings** panel controls when Auto Code asks before downloading a model or saving agent-generated files in the project you opened.
