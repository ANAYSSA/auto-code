# Auto Code

Auto Code is a local agentic development environment (ADE) for Apple Silicon Macs. Open a project, choose a local model, and ask the agent to inspect code, edit files and run checks. Follow-up requests continue in the same conversation and project. The interface is in Russian.

## Install

1. Download `Auto-Code-0.1.0-arm64.dmg` from the [0.1.0 release](https://github.com/ANAYSSA/auto-code/releases/tag/v0.1.0).
2. Open the disk image and drag **Auto Code** into **Applications**.
3. Open **Модели** (Models), choose a model, and download it. **Оптимально для моего Mac** estimates suitable choices from the Mac's chip and installed memory.
4. On **Главная** (Home), open a project with **+**, choose **Агент** (Agent), and send a task. Choose **Чат** (Chat) for conversation without project actions.

Requires macOS 14 or later on Apple Silicon. The app is ad-hoc signed and has not been notarized by Apple. This repository distributes the app; the source repository is private.

## Project work and history

The agent has tools to list, read, search, create and edit project files and run shell commands. It chooses the language and tools from the request and project. Expand an activity row to see the recorded action or command, output and exit status. The timer shows elapsed task time; artifact links open a file or locate a package in Finder.

Local chat history restores messages, project, model, mode, drafts, attachments and activity after restart. Use a chat's menu to rename or delete it. Deleting a chat leaves project files intact. Switching projects creates a separate conversation.

Builds use the tools available on the Mac. Dependency and build commands can access the internet even though inference stays local. Missing SDKs or target-platform runtime support can prevent a build or limit its verification.

## Local models

The catalog contains only community variants labelled **abliterated** or **uncensored** by their publishers. These are publisher claims about modifications to the models; they do not guarantee zero refusals, correct answers or successful autonomous work.

| Model | Download | Intended use |
| --- | --- | --- |
| [Qwen2.5-Coder 7B Abliterated](https://huggingface.co/bartowski/Qwen2.5-Coder-7B-Instruct-abliterated-GGUF) | 4.68 GB | Small programs, code edits and debugging |
| [Huihui Qwen3 8B Abliterated v2](https://huggingface.co/mradermacher/Huihui-Qwen3-8B-abliterated-v2-GGUF) | 5.03 GB | General dialogue and code assistance |
| [Huihui Qwen3 4B Instruct Abliterated](https://huggingface.co/mradermacher/Huihui-Qwen3-4B-Instruct-2507-abliterated-GGUF) | 2.50 GB | Short edits and explanations |
| [Huihui Qwen3 1.7B Abliterated v2](https://huggingface.co/mradermacher/Huihui-Qwen3-1.7B-abliterated-v2-GGUF) | 1.11 GB | Simple snippets and quick questions |
| [Huihui Gemma 4 12B QAT Abliterated](https://huggingface.co/huihui-ai/Huihui-gemma-4-12B-it-qat-q4_0-unquantized-abliterated-GGUF) | 7.38 GB | General assistance, explanation and coding tasks |
| [Huihui Gemma 4 E4B QAT Abliterated](https://huggingface.co/huihui-ai/Huihui-gemma-4-E4B-it-qat-q4_0-unquantized-abliterated-GGUF) | 5.30 GB | General assistance in a smaller model |
| [Huihui Gemma 4 E2B QAT Abliterated](https://huggingface.co/huihui-ai/Huihui-gemma-4-E2B-it-qat-q4_0-unquantized-abliterated-GGUF) | 3.42 GB | Compact dialogue and basic code assistance |
The disk image includes llama.cpp with Metal support. Model weights download separately from pinned revisions and are checked against SHA-256. Installed models run locally without an API key. Browsing the catalog does not download models. Use Delete on an installed model to remove its weights; chat history remains.

## Confirmations

The default mode asks before shell commands, file removal and model downloads. It allows project file creation and edits. **Запрашивать разрешение** asks before each supported change. **Полный доступ** (Full Access) has a separate warning and, once enabled, runs supported actions without repeated prompts. Shell commands then have the normal user's filesystem and network access and may change or delete files outside the project; macOS permissions still apply.

Ask and Default modes restrict command writes to the project and a private temporary cache. A stopped task terminates its workers. Repeated identical tool or command failures stop the task as incomplete instead of being presented as success.

## Validation and limits

Validation on an Apple M5 with 16 GB used the installed Huihui Gemma 4 12B QAT Abliterated model and disposable projects. Test expectations were checked independently; the generated implementations were not hand-repaired by the tester.

| Scenario | Observed result |
| --- | --- |
| Existing Python project | Implemented the missing function; all 6 supplied tests passed. A follow-up preserved every project file and named the actual test command. |
| JavaScript utility | Repaired failures until all 6 supplied tests passed. Additional independent checks passed 13 of 14: sparse arrays remain a known model-generated edge-case bug. A follow-up failed three exact edits without changing the file and was stopped as incomplete. |
| Go calculator / Windows build | The 17 arithmetic and invalid-input cases passed, along with 10 independent host stdin checks. Initial output targeted Windows ARM64; after explicit architecture feedback the model produced and linked a verified Windows AMD64 PE executable. Windows execution was not tested. |
| Owned Android APK | Decoded an owned resource-backed fixture, renamed a method and its call, changed the displayed text, rebuilt and test-signed the APK. Independent re-decoding and signature verification passed; the original APK stayed unchanged. Android execution was not tested. |

These are bounded development examples, not an unattended success rate or a guarantee that every request will work. The model can choose the wrong API, architecture or test command. Read recorded tool output and verify important results. The native runner now also stops repeated identical tool errors, including failed edits, instead of spending the remaining step budget on the same failure.

Native regression checks, ten isolated UI interaction checks and a twelve-check packaged UI smoke test passed.

The agent can make mistakes. A request is limited to 40 tool steps, and older conversation details are shortened to fit the model context. Tool access does not make a small local model equivalent to a larger coding model.

Project by **ANAYSSA**.
