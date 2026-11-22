# Auto Code

Auto Code is a local agentic development environment (ADE) for Apple Silicon Macs. The interface is in Russian. Download [Auto-Code-0.1.1-arm64.dmg](https://github.com/ANAYSSA/auto-code/releases/download/v0.1.1/Auto-Code-0.1.1-arm64.dmg) from the [0.1.1 release](https://github.com/ANAYSSA/auto-code/releases/tag/v0.1.1). Open the disk image and drag Auto Code into Applications. This repository distributes the installer; source code is private.

Open a project, select a local model, and choose **Chat** for questions or **Agent** for work in the project. Both modes accept files and pasted screenshots. Huihui Gemma 4 12B can interpret images through its pinned local vision encoder; other catalog models receive explicit OCR/text-only context. The project agent can inspect the existing source, edit files in place and report what happened during a build or test. A follow-up uses the same open project and recent conversation. For example, in a Python project you can ask it to fix a parser, then ask which test covers the change.

**Experimental:** the tested local model still fails some basic coding tasks. Read the validation results below before relying on autonomous completion.

## Model catalog

The catalog contains community variants labelled **abliterated** or **uncensored** by their publishers. These labels describe the publishers' modifications; they do not guarantee that every request will be accepted or completed correctly.

| Model | Download | Intended use |
| --- | --- | --- |
| [Qwen2.5-Coder 7B Abliterated](https://huggingface.co/bartowski/Qwen2.5-Coder-7B-Instruct-abliterated-GGUF) | 4.68 GB | Small programs, code edits and debugging |
| [Huihui Qwen3 8B Abliterated v2](https://huggingface.co/mradermacher/Huihui-Qwen3-8B-abliterated-v2-GGUF) | 5.03 GB | General dialogue and code assistance |
| [Huihui Qwen3 4B Instruct Abliterated](https://huggingface.co/mradermacher/Huihui-Qwen3-4B-Instruct-2507-abliterated-GGUF) | 2.50 GB | Short edits and explanations |
| [Huihui Qwen3 1.7B Abliterated v2](https://huggingface.co/mradermacher/Huihui-Qwen3-1.7B-abliterated-v2-GGUF) | 1.11 GB | Simple snippets and quick questions |
| [Huihui Gemma 4 12B QAT Abliterated](https://huggingface.co/huihui-ai/Huihui-gemma-4-12B-it-qat-q4_0-unquantized-abliterated-GGUF) | 7.38 GB | General assistance, explanation and coding tasks |
| [Huihui Gemma 4 E4B QAT Abliterated](https://huggingface.co/huihui-ai/Huihui-gemma-4-E4B-it-qat-q4_0-unquantized-abliterated-GGUF) | 5.30 GB | General assistance in a smaller model |
| [Huihui Gemma 4 E2B QAT Abliterated](https://huggingface.co/huihui-ai/Huihui-gemma-4-E2B-it-qat-q4_0-unquantized-abliterated-GGUF) | 3.42 GB | Compact dialogue and basic code assistance |

Open Models to search by name or task. The Best for my Mac filter checks the chip architecture and installed memory, reserves headroom for the system and inference, and ranks the coding models that fit. The recommendation estimates memory use. Actual speed and capacity depend on other running apps and prompt length.

Model weights are downloaded on request from pinned revisions, checked against SHA-256, and stored under `~/Library/Application Support/Auto Code/Models`. After a download, llama.cpp runs the model on your Mac through a private Unix-domain socket. The bundled runtime supports Metal acceleration. The model selector remembers the last choice. Installed models have a Delete action with a confirmation; deleting weights does not delete chat history. Models are never downloaded just by browsing the catalog.

## Using the app

The sidebar stores local chat history. Use the chat menu to rename or delete a conversation. Each chat restores its project, selected model, mode, attachments, draft, messages and recorded task activity after restart. Starting a new dialog gives it a separate conversation context. If attachment cleanup fails after deleting a chat, the new active chat is restored consistently and the existing status area reports the cleanup warning. Changing projects starts a new chat; deleting a chat leaves project files intact.

- Open a project with **+**. Attach files with **+** or **@**, or paste a copied image/file with **Command+V** in the message field. Plain text paste stays normal. Up to eight files per message, 64 MiB each; the per-chat attachment store is limited to 256 MiB. PDFs provide extracted text, and binary files can be inspected by project tools. Managed copies remain with the chat; deleting the chat removes those copies, not the originals.
- Send a task in **Agent** mode. The timer shows elapsed time. Expand an activity row to see the file involved or the command, its recorded output and exit status. The final reply links the files reported by the project runner. Click a text file to open it in the editor or a packaged artifact to locate it in Finder.
- Builds depend on the tools and dependencies available on your Mac. The inference model stays local. Project commands can access the network, for example to install dependencies; a local model does not make those external commands offline. A Python test suite needs its Python environment. A Windows executable needs a compatible target toolchain. Packaging and signing an Apple app require the appropriate SDK and signing configuration.
- If a check cannot finish, expand its activity row to see its output and exit status. An incomplete task retains its file links and can be discussed in a follow-up.
- The GitHub and Vercel integrations display local project metadata.

## Verification and app control

The agent uses native model function calls for its eleven tools. Tool definitions are included in the model context, and recorded tool results are paired with their calls. Malformed or unknown calls are rejected before execution and returned to the model with specific function/field/type guidance for correction, without echoing argument values or private state. Matching native assistant state is preserved during the live tool sequence, without adding private reasoning to chat history or activity. It is discarded across new user turns and is not reused when the recorded history no longer matches.

The agent has a separate `check` tool. A completed task requires passing verification during that request against the final project state. Changes and failed actions invalidate earlier verification. Comment-only commands, echoed success and syntax-only checks cannot satisfy completion. Modified JavaScript, Python and JSON files receive a syntax preflight before functional checks. Informational replies use a neutral “Answer without execution or verification” status, not a completed-task badge. Failed exact edits trigger a re-read and replacement strategy before the bounded loop stops. Replacing an existing file requires an unshortened full read; partial reads allow precise edits. A one-time fresh-context recovery review can interrupt repeated actions before any edit, using observed source and actual command output without treating diagnostic output as verification.

Before accepting completion, a fresh model context compares the request, bounded source excerpts and recorded checks. Concrete findings return to the agent for reproduction, repair and another check. Unresolved findings or files changed during review prevent completion. This review uses the same local model and is an additional check, not an independent guarantee of correctness.

The `browser` tool launches Google Chrome with a disposable profile, loads a Manifest V3 extension or opens a page, and performs mouse/keyboard interactions and explicit DOM assertions. Actual extension CSP, runtime errors and failed assertions cause failure. Manifest-declared extension popups cannot be tested as ordinary local HTML pages, which would bypass the extension CSP. Inspecting a page without assertions is not a verified result. This test profile does not install an extension in your everyday Chrome profile. Browser testing requires an installed Google Chrome and Node.js 20 or later.

The `desktop` tool works with accessible macOS applications, including Finder, TextEdit and browsers. It lists running apps, inspects accessible controls, clicks observed elements, fills editable fields, sends keyboard shortcuts, opens files/URLs and compares observed values. References expire after UI actions; the agent must inspect again. macOS Accessibility permission is required separately from Auto Code's Full Access setting. Custom canvases and inaccessible controls may not expose usable elements. Opening a page or sending a click is not proof of installation; the agent must inspect and verify the result.

For Huihui Gemma 4 12B, the first image task downloads a 175 MB vision companion from the same pinned publisher revision and verifies SHA-256. Image interpretation then runs locally. The encoder is removed with that model. Other models are not downloaded automatically. Gemma agent calls use a bounded thinking budget alongside native function calls; private reasoning is not inserted into chat history.

## Action confirmations

The lower-left **…** menu shows the installed app version beside Auto Code. Open **Settings** from that menu or press **Command+,**. These settings apply to actions requested by the project agent and to model downloads. Opening or saving a file yourself in the editor is a separate action.

| Setting (English translation) | When Auto Code asks |
| --- | --- |
| Ask | Before creating, editing or removing a file, running a command/browser test, controlling an app, or downloading a model. |
| Default | Before running a command/browser test, controlling an app, removing a file, or downloading a model. The agent can create and edit project files directly. |
| Full Access | Enabling this mode requires a separate confirmation. It then performs supported actions without an additional prompt for each one. Shell commands run with your normal user filesystem and network access, subject to macOS permissions. They can change or delete files outside the project; use this mode only when you accept that risk. |

In Ask and Default modes, shell writes are restricted to the open project and a private temporary cache. Full Access removes that shell sandbox. File editor tools always address paths inside the open project. Commands have a timeout and stop when you cancel the task. The browser tool runs its fixed checker outside that shell sandbox to start Chrome, confines file targets/screenshots to the project and uses a disposable profile. Desktop actions use ordinary macOS user permissions.

## Distribution

Requires macOS 14 or later on Apple Silicon. The app uses an ad-hoc signature and is not Apple-notarized. Model weights are not bundled in the installer.

## Validation

Version 0.1.1 passed 13 native check programs and 13 isolated Web/history checks. Real Chrome regressions covered extension CSP, JavaScript exceptions, assertions, input and cleanup. An owned AppKit fixture verified actual clicks, keyboard selection, text entry and UI assertions. Native picker, text paste, copied-image paste and attachment restoration were exercised in the app. The packaged DMG was mounted and its version, signature, executable and resource hashes checked.

Real-model checks used the installed Huihui Gemma 4 12B QAT Abliterated model on an M5 Mac with 16 GB memory. Local vision correctly identified the colors and positions in a text-free fixture. In an earlier Python repair run, the model corrected the implementation, caught and repaired a missing test import, and passed its tests plus 15 independent behavioral checks; that workflow still ended blocked on malformed completion calls. More specific call-validation feedback was added afterward, but the queued follow-up was not run.

The final calculator run did not complete within its 600-second validation budget. An independent real-Chrome check still failed the first assertion, `1 + 1 = 2`: one of seven planned assertions was attempted, none passed, and the other six were not executed. The popup still used `eval` under Manifest V3 CSP. The final JS utility run was interrupted at the user's request because the Mac was heating up; its final independent check was not run. Remaining model work was stopped before publication.

These results validate the tool infrastructure, not reliable general-purpose autonomous coding. The tested model still fails basic tasks. This release is experimental; improving model task reliability remains unfinished.

Checks are bounded examples, not a guarantee that any request will succeed. The host verifies executed evidence and rejects obvious no-ops, but it cannot prove that arbitrary model-written tests cover every requirement. A 12B model may still choose a weak implementation or overlook an edge case.

When older history is shortened, the agent starts a fresh model turn with the current request and bounded recorded evidence, instead of continuing an incomplete native tool turn.

A request has a maximum of 40 tool steps. Follow-up messages keep the same project and recent history, while older details are shortened to fit the local model's context. Runtime and compiler availability affect which artifacts a model can produce.

The tool loop follows the model/action/result pattern described in [OpenAI's Codex agent loop](https://openai.com/index/unrolling-the-codex-agent-loop/) and [OpenCode's tool documentation](https://opencode.ai/docs/tools/).
