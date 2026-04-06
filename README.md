<div align="center">

# Free Claude Code

### Use Claude Code CLI & VSCode for free. No Anthropic API key required.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?style=for-the-badge)](https://opensource.org/licenses/MIT)
[![Python 3.14](https://img.shields.io/badge/python-3.14-3776ab.svg?style=for-the-badge&logo=python&logoColor=white)](https://www.python.org/downloads/)
[![uv](https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/astral-sh/uv/main/assets/badge/v0.json&style=for-the-badge)](https://github.com/astral-sh/uv)
[![Type checking: Ty](https://img.shields.io/badge/type%20checking-ty-ffcc00.svg?style=for-the-badge)](https://pypi.org/project/ty/)
[![Code style: Ruff](https://img.shields.io/badge/code%20formatting-ruff-f5a623.svg?style=for-the-badge)](https://github.com/astral-sh/ruff)
[![Logging: Loguru](https://img.shields.io/badge/logging-loguru-4ecdc4.svg?style=for-the-badge)](https://github.com/Delgan/loguru)

Free Claude Code CLI & VSCode (No Anthropic API Key Required)
This lightweight proxy intercepts Claude Code's Anthropic API calls and reroutes them to your provider of choice:

**NVIDIA NIM** (40 req/min free) : High-speed access with a free tier of 40 requests/minute.

**OpenRouter** (hundreds of models) Tap into hundreds of different AI models.

**LM Studio** (fully local) Run everything fully offline and locally..

[Features](#features) · [Quick Start](#quick-start) · [How It Works](#how-it-works) · [Discord Bot](#discord-bot) · [Configuration](#configuration)

---

</div>

<div align="center">
  <img src="pic.png" alt="Free Claude Code in action" width="700">
  <p><em>Claude Code running via NVIDIA NIM, completely free</em></p>
</div>

## Features

| Feature                    | Description                                                                                                          |
| -------------------------- | -------------------------------------------------------------------------------------------------------------------- |
| **Zero Cost**              | 40 req/min free on NVIDIA NIM. Free models on OpenRouter. Fully local with LM Studio                                 |
| **Drop-in Replacement**    | Set 2 env vars. No modifications to Claude Code CLI or VSCode extension needed                                       |
| **3 Providers**            | NVIDIA NIM, OpenRouter (hundreds of models), LM Studio (local & offline)                                             |
| **Per-Model Mapping**      | Route Opus / Sonnet / Haiku requests to different models and providers. Mix providers freely per model               |
| **Thinking Token Support** | Parses `<think>` tags and `reasoning_content` into native Claude thinking blocks                                     |
| **Heuristic Tool Parser**  | Models outputting tool calls as text are auto-parsed into structured tool use                                        |
| **Request Optimization**   | 5 categories of trivial API calls intercepted locally, saving quota and latency                                      |
| **Discord Bot**            | Remote autonomous coding with tree-based threading, session persistence, and live progress (Telegram also supported) |
| **Smart Rate Limiting**    | Proactive rolling-window throttle + reactive 429 exponential backoff + optional concurrency cap across all providers |
| **Subagent Control**       | Task tool interception forces `run_in_background=False`. No runaway subagents                                        |
| **Extensible**             | Clean `BaseProvider` and `MessagingPlatform` ABCs. Add new providers or platforms easily                             |

## Quick Start

### Prerequisites

1. Get an API key (or use LM Studio locally):
   - **NVIDIA NIM**: [build.nvidia.com/settings/api-keys](https://build.nvidia.com/settings/api-keys)
   - **OpenRouter**: [openrouter.ai/keys](https://openrouter.ai/keys)
   - **LM Studio**: No API key needed. Run locally with [LM Studio](https://lmstudio.ai)
2. Install [Claude Code](https://github.com/anthropics/claude-code)
3. Install [uv](https://github.com/astral-sh/uv)
4. Update uv if already installed: `uv self update`

### Clone & Configure

```bash
git clone https://github.com/rohit17vispute/RohitV-ClaudeAi-Free.git
cd free-claude-code
cp .env.example .env
```

Choose your provider and edit `.env`:

<details>
<summary><b>NVIDIA NIM</b> (40 req/min free, recommended)</summary>

```dotenv
NVIDIA_NIM_API_KEY="nvapi-your-key-here"

MODEL_OPUS="nvidia_nim/z-ai/glm4.7"
MODEL_SONNET="nvidia_nim/moonshotai/kimi-k2-thinking"
MODEL_HAIKU="nvidia_nim/stepfun-ai/step-3.5-flash"
MODEL="nvidia_nim/z-ai/glm4.7"                     # fallback
```

</details>

<details>
<summary><b>OpenRouter</b> (hundreds of models)</summary>

```dotenv
OPENROUTER_API_KEY="sk-or-your-key-here"

MODEL_OPUS="open_router/deepseek/deepseek-r1-0528:free"
MODEL_SONNET="open_router/openai/gpt-oss-120b:free"
MODEL_HAIKU="open_router/stepfun/step-3.5-flash:free"
MODEL="open_router/stepfun/step-3.5-flash:free"     # fallback
```

</details>

<details>
<summary><b>LM Studio</b> (fully local, no API key)</summary>

```dotenv
MODEL_OPUS="lmstudio/unsloth/MiniMax-M2.5-GGUF"
MODEL_SONNET="lmstudio/unsloth/Qwen3.5-35B-A3B-GGUF"
MODEL_HAIKU="lmstudio/unsloth/GLM-4.7-Flash-GGUF"
MODEL="lmstudio/unsloth/GLM-4.7-Flash-GGUF"         # fallback
```

</details>

<details>
<summary><b>Mix providers</b> (use multiple providers together)</summary>

Each `MODEL_*` variable can use a different provider. `MODEL` is the fallback for unrecognized Claude models.

```dotenv
NVIDIA_NIM_API_KEY="nvapi-your-key-here"
OPENROUTER_API_KEY="sk-or-your-key-here"

MODEL_OPUS="nvidia_nim/moonshotai/kimi-k2.5"
MODEL_SONNET="open_router/deepseek/deepseek-r1-0528:free"
MODEL_HAIKU="lmstudio/unsloth/GLM-4.7-Flash-GGUF"
MODEL="nvidia_nim/z-ai/glm4.7"                      # fallback
```

</details>

### Run It

**Terminal 1:** Start the proxy server:

```bash
uv run uvicorn server:app --host 0.0.0.0 --port 8082
```

**Terminal 2:** Run Claude Code:

```bash
ANTHROPIC_AUTH_TOKEN="freecc" ANTHROPIC_BASE_URL="http://localhost:8082" claude
```
```powershell
$env:ANTHROPIC_AUTH_TOKEN="freecc"; $env:ANTHROPIC_BASE_URL="http://localhost:8082"; claude
```

That's it! Claude Code now uses your configured provider for free.

<details>
<summary><b>VSCode Extension Setup</b></summary>

1. Start the proxy server (same as above).
2. Open Settings (`Ctrl + ,`) and search for `claude-code.environmentVariables`.
3. Click **Edit in settings.json** and add:

```json
"claude-code.environmentVariables": [
  { "name": "ANTHROPIC_BASE_URL", "value": "http://localhost:8082" },
  { "name": "ANTHROPIC_AUTH_TOKEN", "value": "freecc" }
]
```

4. Reload extensions.
5. **If you see the login screen** ("How do you want to log in?"): Click **Anthropic Console**, then authorize. The extension will start working. You may be redirected to buy credits in the browser; ignore it, the extension already works.

To switch back to Anthropic models, comment out the added block and reload extensions.

</details>

<details>
<summary><b>Multi-Model Support (Model Picker)</b></summary>

`claude-pick` is an interactive model selector that lets you choose any model from your active provider each time you launch Claude, without editing `MODEL` in `.env`.

https://github.com/user-attachments/assets/9a33c316-90f8-4418-9650-97e7d33ad645

**1. Install [fzf](https://github.com/junegunn/fzf)** (highly recommended for the interactive picker):

```bash
brew install fzf        # macOS/Linux
```

**2. Add the alias to `~/.zshrc` or `~/.bashrc`:**

```bash
# Use the absolute path to your cloned repo
alias claude-pick="/absolute/path/to/free-claude-code/claude-pick"
```

Then reload your shell (`source ~/.zshrc` or `source ~/.bashrc`) and run `claude-pick` to pick a model and launch Claude.

**Skip the picker with a fixed model** (no picker needed):

```bash
alias claude-kimi='ANTHROPIC_BASE_URL="http://localhost:8082" ANTHROPIC_AUTH_TOKEN="freecc:moonshotai/kimi-k2.5" claude'
```

</details>

---

## How It Works

```
┌─────────────────┐        ┌──────────────────────┐        ┌──────────────────┐
│  Claude Code    │───────>│  Free Claude Code    │───────>│  LLM Provider    │
│  CLI / VSCode   │<───────│  Proxy (:8082)       │<───────│  NIM / OR / LMS  │
└─────────────────┘        └──────────────────────┘        └──────────────────┘
   Anthropic API                     │                       OpenAI-compatible
   format (SSE)              ┌───────┴────────┐                format (SSE)
                             │ Optimizations  │
                             ├────────────────┤
                             │ Quota probes   │
                             │ Title gen skip │
                             │ Prefix detect  │
                             │ Suggestion skip│
                             │ Filepath mock  │
                             └────────────────┘
```

- **Transparent proxy**: Claude Code sends standard Anthropic API requests to the proxy server
- **Per-model routing**: Opus / Sonnet / Haiku requests are resolved to their model-specific backend and provider, with the default `MODEL` as fallback
- **Request optimization**: 5 categories of trivial requests (quota probes, title generation, prefix detection, suggestions, filepath extraction) are intercepted and responded to instantly without using API quota
- **Format translation**: real requests are translated from Anthropic format to the provider's OpenAI-compatible format and streamed back
- **Thinking tokens**: `<think>` tags and `reasoning_content` fields are converted into native Claude thinking blocks so Claude Code renders them correctly

---

## Providers

| Provider       | Cost         | Rate Limit | Models                            | Best For                             |
| -------------- | ------------ | ---------- | --------------------------------- | ------------------------------------ |
| **NVIDIA NIM** | Free         | 40 req/min | Kimi K2, GLM5, Devstral, MiniMax  | Daily driver, generous free tier     |
| **OpenRouter** | Free / Paid  | Varies     | 200+ (GPT-4o, Claude, Step, etc.) | Model variety, fallback options      |
| **LM Studio**  | Free (local) | Unlimited  | Any GGUF model                    | Privacy, offline use, no rate limits |

Switch providers by changing `MODEL` in `.env`. Use the prefix format `provider/model/name`. Invalid prefix causes an error.

| Provider   | MODEL prefix      | API Key Variable     | Base URL                      |
| ---------- | ----------------- | -------------------- | ----------------------------- |
| NVIDIA NIM | `nvidia_nim/...`  | `NVIDIA_NIM_API_KEY` | `integrate.api.nvidia.com/v1` |
| OpenRouter | `open_router/...` | `OPENROUTER_API_KEY` | `openrouter.ai/api/v1`        |
| LM Studio  | `lmstudio/...`    | (none)               | `localhost:1234/v1`           |

LM Studio runs locally. Start the server in the Developer tab or via `lms server start`, load a model, and set `MODEL` to the model identifier.

---

## Models

<details>
<summary><b>NVIDIA NIM</b></summary>

Full list in [`nvidia_nim_models.json`](nvidia_nim_models.json).

Popular models:

- `nvidia_nim/minimaxai/minimax-m2.5`
- `nvidia_nim/qwen/qwen3.5-397b-a17b`
- `nvidia_nim/z-ai/glm5`
- `nvidia_nim/stepfun-ai/step-3.5-flash`
- `nvidia_nim/moonshotai/kimi-k2.5`

Browse: [build.nvidia.com](https://build.nvidia.com/explore/discover)

Update model list:

```bash
curl "https://integrate.api.nvidia.com/v1/models" > nvidia_nim_models.json
```

</details>

<details>
<summary><b>OpenRouter</b></summary>

Hundreds of models from StepFun, OpenAI, Anthropic, Google, and more.

Popular models:

- `open_router/stepfun/step-3.5-flash:free`
- `open_router/deepseek/deepseek-r1-0528:free`
- `open_router/openai/gpt-oss-120b:free`

Browse: [openrouter.ai/models](https://openrouter.ai/models)

Browse free models: [https://openrouter.ai/collections/free-models](https://openrouter.ai/collections/free-models)

</details>

<details>
<summary><b>LM Studio</b></summary>

Run models locally with [LM Studio](https://lmstudio.ai). Load a model in the Chat or Developer tab, then set `MODEL` to its identifier.

Examples (native tool-use support):

- `LiquidAI/LFM2-24B-A2B-GGUF`
- `unsloth/MiniMax-M2.5-GGUF`
- `unsloth/GLM-4.7-Flash-GGUF`
- `unsloth/Qwen3.5-35B-A3B-GGUF`
- `LocoreMind/LocoOperator-4B`

Browse: [model.lmstudio.ai](https://model.lmstudio.ai)

</details>

---

## Configuration

| Variable                          | Description                                                                        | Default                                           |
| --------------------------------- | ---------------------------------------------------------------------------------- | ------------------------------------------------- |
| `MODEL`                           | Fallback model (prefix format: `provider/model/name`; invalid prefix causes error) | `nvidia_nim/stepfun-ai/step-3.5-flash`            |
| `MODEL_OPUS`                      | Model for Claude Opus requests (optional, falls back to `MODEL`)                   | `nvidia_nim/z-ai/glm4.7`                          |
| `MODEL_SONNET`                    | Model for Claude Sonnet requests (optional, falls back to `MODEL`)                 | `open_router/arcee-ai/trinity-large-preview:free` |
| `MODEL_HAIKU`                     | Model for Claude Haiku requests (optional, falls back to `MODEL`)                  | `open_router/stepfun/step-3.5-flash:free`         |
| `NVIDIA_NIM_API_KEY`              | NVIDIA API key (NIM provider)                                                      | required                                          |
| `OPENROUTER_API_KEY`              | OpenRouter API key (OpenRouter provider)                                           | required                                          |
| `LM_STUDIO_BASE_URL`              | LM Studio server URL                                                               | `http://localhost:1234/v1`                        |
| `PROVIDER_RATE_LIMIT`             | LLM API requests per window                                                        | `40`                                              |
| `PROVIDER_RATE_WINDOW`            | Rate limit window (seconds)                                                        | `60`                                              |
| `PROVIDER_MAX_CONCURRENCY`        | Max simultaneous open provider streams                                             | `5`                                               |
| `HTTP_READ_TIMEOUT`               | Read timeout for provider API requests (seconds)                                   | `120`                                             |
| `HTTP_WRITE_TIMEOUT`              | Write timeout for provider API requests (seconds)                                  | `10`                                              |
| `HTTP_CONNECT_TIMEOUT`            | Connect timeout for provider API requests (seconds)                                | `2`                                               |
| `FAST_PREFIX_DETECTION`           | Enable fast prefix detection                                                       | `true`                                            |
| `ENABLE_NETWORK_PROBE_MOCK`       | Enable network probe mock                                                          | `true`                                            |
| `ENABLE_TITLE_GENERATION_SKIP`    | Skip title generation                                                              | `true`                                            |
| `ENABLE_SUGGESTION_MODE_SKIP`     | Skip suggestion mode                                                               | `true`                                            |
| `ENABLE_FILEPATH_EXTRACTION_MOCK` | Enable filepath extraction mock                                                    | `true`                                            |
| `MESSAGING_PLATFORM`              | Messaging platform: `discord` or `telegram`                                        | `discord`                                         |
| `VOICE_NOTE_ENABLED`              | Enable voice note handling                                                         | `true`                                            |
| `WHISPER_MODEL`                   | Local Whisper model size                                                           | `base`                                            |
| `WHISPER_DEVICE`                  | `cpu` \| `cuda`                                                                    | `cpu`                                             |
| `MESSAGING_RATE_LIMIT`            | Messaging messages per window                                                      | `1`                                               |
| `MESSAGING_RATE_WINDOW`           | Messaging window (seconds)                                                         | `1`                                               |
| `CLAUDE_WORKSPACE`                | Directory for agent workspace                                                      | `./agent_workspace`                               |
| `ALLOWED_DIR`                     | Allowed directories for agent                                                      | `""`                                              |

See [`.env.example`](.env.example) for all supported parameters.

---

## Development

### Project Structure

```
free-claude-code/
├── server.py              # Entry point
├── api/                   # FastAPI routes, request detection, optimization handlers
├── providers/             # BaseProvider, OpenAICompatibleProvider, NIM, OpenRouter, LM Studio
│   └── common/            # Shared utils (SSE builder, message converter, parsers, error mapping)
├── messaging/             # MessagingPlatform ABC + Discord/Telegram bots, session management
├── config/                # Settings, NIM config, logging
├── cli/                   # CLI session and process management
└── tests/                 # Pytest test suite
```

### Commands

```bash
uv run ruff format     # Format code
uv run ruff check      # Code style checking
uv run ty check        # Type checking
uv run pytest          # Run tests
```

---

## Extending

### Adding a Provider

For **OpenAI-compatible APIs** (Groq, Together AI, etc.), extend `OpenAICompatibleProvider`:

```python
from providers.openai_compat import OpenAICompatibleProvider
from providers.base import ProviderConfig

class MyProvider(OpenAICompatibleProvider):
    def __init__(self, config: ProviderConfig):
        super().__init__(config, provider_name="MYPROVIDER",
                         base_url="https://api.example.com/v1", api_key=config.api_key)

    def _build_request_body(self, request):
        return build_request_body(request)  # Your request builder
```

For **fully custom APIs**, extend `BaseProvider` directly:

```python
from providers.base import BaseProvider, ProviderConfig

class MyProvider(BaseProvider):
    async def stream_response(self, request, input_tokens=0, *, request_id=None):
        # Yield Anthropic SSE format events
        ...
```

### Adding a Messaging Platform

Extend `MessagingPlatform` in `messaging/` to add Slack or other platforms:

```python
from messaging.base import MessagingPlatform

class MyPlatform(MessagingPlatform):
    async def start(self):
        # Initialize connection
        ...

    async def stop(self):
        # Cleanup
        ...

    async def send_message(self, chat_id, text, reply_to=None, parse_mode=None, message_thread_id=None):
        # Send a message
        ...

    async def edit_message(self, chat_id, message_id, text, parse_mode=None):
        # Edit an existing message
        ...

    def on_message(self, handler):
        # Register callback for incoming messages
        ...
```

---

## Contributing 
- Add new LLM providers (Grok, Together AI, etc.)
- Add new messaging platforms (Slack, etc.)
- New and interesting features

```bash
# Fork the repo, then:
git checkout -b my-feature
# Make your changes
uv run ruff format && uv run ruff check && uv run ty check && uv run pytest
# Open a pull request
```

---

## License

This project is licensed under the **MIT License**. See the [LICENSE](LICENSE) file for details.


