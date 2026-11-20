# Private AI Ready

[![Private AI Ready](guia/assets/banner-en.jpg)](https://inematds.github.io/ia-privada-pronta/guia/en/)

**🇧🇷 [Português](../../README.md) · 🇺🇸 [English](README.md) · 🇪🇸 [Español](../es/)**

[🎬 Video: talk to your AI without internet (audio in Portuguese).](https://www.youtube.com/watch?v=TJ9omYZdIrE)

Full guide: https://inematds.github.io/ia-privada-pronta/guia/en/

**Real Linux validation:** execution without external network access, a conversation with context, and shutdown of the dedicated processes were confirmed. All 14 software tests passed. The lightweight model got the document calculation wrong; the result is recorded without changing the answer key. See [the full results](../../verificacao/STATUS.md).

## What it is

This free kit lets you chat with an AI on your own computer. It is for people who want local processing without an AI company receiving each request. It includes model setup, chat, file reading, and a Linux mode that blocks network access for the process. You need Python, Ollama, and enough memory for the model. Real test results and limitations are on this page.

This kit lets you chat with an AI on your own computer, without an external inference provider. Its focus is keeping processing local: a dedicated service on a local address, cloud access disabled, and, on Linux, a mode that removes network access from the process. The default model is Llama 3.2 3B; it retains the model's own restrictions.

**You finish by chatting with a real model.** The kit includes the launcher, model profile, weight setup, chat with context, file-based questions, an offline Linux mode, and an executable check. No key, model subscription, or extra Python libraries are needed.

## Requirements

- Git to clone, or [download the ZIP](https://github.com/inematds/ia-privada-pronta/archive/refs/heads/main.zip) and extract the folder.
- Python 3.10 or later: [official installer](https://www.python.org/downloads/).
- [Ollama installed](https://ollama.com/download) on Linux, macOS, or Windows.
- For the default model: approximately **2 GB to download**, **8 GB of RAM recommended**, free space for the weights, and resources for the system. Default context: 2048; actual usage depends on the runtime. A GPU is optional: CPU works, with different performance.
- On Linux, for offline mode: `bubblewrap` and `iproute2`. This mode requires user namespaces to be allowed by the system.

Check before you begin:

```bash
python3 --version
ollama --version
```

On Windows, use `py` instead of `python3` if needed. After installing Python or Ollama, open a new terminal. The kit does not install packages with elevated privileges or change the Ollama used by other projects.

## Install and chat

```bash
git clone https://github.com/inematds/ia-privada-pronta.git
cd ia-privada-pronta
python3 ia.py diagnostico
python3 ia.py preparar
python3 ia.py verificar
python3 ia.py conversar
```

`preparar` uses the internet to download or check the model weights. After that, `verificar` and `conversar` use the downloaded local model without automatically downloading another one. The weights are stored by Ollama itself, not in the repository.

In the conversation, type `/limpar` to reset the context and `/sair` to exit. The kit keeps only a few recent messages in memory and does not save chat history. When you exit, it unloads the model and shuts down only its own instance and child processes. The terminal or operating system may still record what appears on screen.

## Linux: chat with network access blocked for the process

After preparing the weights, install these two system tools if they are missing:

```bash
sudo apt install bubblewrap iproute2
bash sem-rede.sh verificar
bash sem-rede.sh conversar
```

The launcher creates a separate network with only the local interface `lo`. The model and client continue communicating inside it; there is no interface for reaching the internet. The computer remains normally connected for other programs. This mode isolates the network, not the user's files; it is not a virtual machine.

On macOS/Windows, local chat works through `ia.py`; the offline launcher is Linux-specific. To check manually on those systems, prepare first, disconnect Wi-Fi/cable, and run `verificar`. We do not claim this test was performed on systems we did not use.

## One question and files

```bash
python3 ia.py perguntar "Escreva três ideias para organizar uma reunião curta."
python3 ia.py perguntar --arquivo exemplos/primeiro-pedido.txt
```

For sensitive text, prefer `--arquivo` or standard input: arguments typed on the command line may remain in shell history. Answers are text; the kit does not execute commands suggested by AI.

## How to tell it worked

```bash
python3 ia.py verificar
```

The check sends a real question to the model (`17 + 25`) and expects `42`. It saves the result to `.estado/verificacao.json`, including the weights digest, generated tokens, and network interfaces. Exit code 0 means this case passed; 1 indicates a different answer; 2 indicates a setup/execution error. A correct answer does not prove general competence or absence of refusals.

Also read [verificacao/STATUS.md](../../verificacao/STATUS.md): it distinguishes software tests from inference actually run. The answer key for a second exercise is in `exemplos/documento-ficticio.txt`.

## What each choice means

**Privacy:** who receives the data and where the task runs. In this kit, the request goes only to the dedicated Ollama instance at `127.0.0.1`. Cloud access and external tools are disabled. Linux offline mode adds an operating-system restriction.

**Uncensored:** a model published by its maintainer with fewer refusals. This term does not promise acceptance of every request, correct answers, or a replacement for human review. The kit does not add topic filters or automatically modify the model.

## Model, version, and memory

The profile uses **`llama3.2:latest`**, checked on 15/11/2026. `perfil.json` records the expected weights digest to prevent a silent model change. If the maintainer changes the tag and the digest no longer matches, execution stops: check the new version before updating `digest_esperado`. Do not remove the check just to make it pass.

The profile limits context to 2048 and the response to 512 tokens. The conversation has a short window; long documents need to be split up. First-response time includes loading the weights. If memory runs short, use a machine with enough capacity or consciously choose another local model, adjusting its name and digest. There is no cloud fallback.

## Important behavior

- Dedicated instance, exclusive port, and cloud access disabled. A busy port causes an error; the kit never silently connects to another service.
- Inherited proxies do not receive requests, and HTTP redirects are rejected.
- A remote model name, missing weights, a different digest, or an empty response stops execution.
- No connectors, web search, agents that execute tools, or automatic publishing.
- This workflow's privacy does not protect against malware, computer administrators, screen capture, backups, or a compromised runtime.

## Software tests

```bash
python3 -m unittest discover -s tests -v
```

These tests use a simulated runtime and do not count as a model test. The real AI test is `verificar`.

## Tool documentation

[Ollama: local execution and cloud](https://docs.ollama.com/faq) · [Local chat API](https://docs.ollama.com/api/chat) · [Default model](https://ollama.com/library/llama3.2).

Kit code: MIT. The runtime and weights have their own licenses; weights are not redistributed here.

The kit's code and messages are in Portuguese.
## If you downloaded the ZIP

Extract the folder. On Windows, open the folder containing ia.py, click the address bar, type powershell, and press Enter. Run the Python commands in the guide, using py if needed. The preparar.cmd and conversar.cmd shortcuts also work with a double-click. On Linux/macOS, open Terminal and use cd to enter the extracted folder; the path depends on where you saved the ZIP. Do not run git clone if you chose the ZIP.

Runtime is the program that runs the model; digest is the exact identification of the weights; a network namespace is a separate network for the process on Linux. If the digest changes, the error shows the expected and found values: confirm the version on the tag's official page before deciding to update digest_esperado in perfil.json. Without confirmation, keep the protection.
