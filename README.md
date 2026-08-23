# Slap Battles Helper

A tiny screenshot-to-answer helper. Press one hotkey and it grabs the screen,
sends it to **Gemini** or **OpenAI**, and shows the answer in a clean overlay box
at the top of the screen — asking the model to reply with **numbers only**, so the
answer is ready to type straight in.

> Windows-first (works anywhere Python runs). Everything is local except the one
> API call to your chosen provider.

---

## ⚠ Read first

Automating or assisting in a game can be **against its rules** and your account
**may be banned**. Use it only on an account you're willing to lose. The author is
**not responsible** for any bans or consequences. Do **not** use it to harass
anyone or for any malicious purpose. You use it entirely **at your own risk**.

---

## Setup (start here)

1. Install [Python 3.10+](https://www.python.org/downloads/) (tick **"Add to PATH"**).
2. Double-click **`setup.bat`**. On the first run it installs the dependencies, then
   opens a settings window where you:
   - pick **Gemini** or **OpenAI** and paste your **API key**,
   - (optional) set a **model** — defaults are `gemini-2.0-flash` / `gpt-4o-mini`,
   - choose the **screenshot hotkey** (default **End**) and a **quit** hotkey (F8),
   - tweak the **prompt** (default asks for numbers only),
   - press **Save & create run.bat**.
3. Launch the helper with **`run.bat`**.

Your API key lives in `app/config/settings.json`, which is **git-ignored** and never
committed.

## Providers & where to get a key

Pick one in setup with a **Quick preset** button. `gemini` uses Google's native API;
every other preset is an **OpenAI-compatible** endpoint (same code, just a different
base URL), so you can plug in whichever free key you have:

| Preset | Free tier | Get a key |
|---|---|---|
| **Gemini** ⭐ | yes, no card | [aistudio.google.com/apikey](https://aistudio.google.com/apikey) |
| **NVIDIA NIM** | yes, no card (`nvapi-…`) | [build.nvidia.com](https://build.nvidia.com) → API keys |
| **Groq** | yes (fast) | [console.groq.com/keys](https://console.groq.com/keys) |
| **Mistral** | yes (Pixtral) | [console.mistral.ai](https://console.mistral.ai) |
| **OpenRouter** | 50 req/day | [openrouter.ai/keys](https://openrouter.ai/keys) |
| **OpenAI** | paid (needs billing) | [platform.openai.com/api-keys](https://platform.openai.com/api-keys) |

**Gemini** is the easiest and most accurate for reading tasks. The model ids in the
presets are suggestions — if a provider renames a vision model, edit the **Model** field
(pick a *vision* model or the screenshot won't be read).

### Fully local / offline (Ollama)

Run everything on your own machine, no keys, no cloud:

1. Install [Ollama](https://ollama.com), then pull a **vision** model:
   ```bash
   ollama pull qwen2.5vl:7b
   ```
2. In setup click the **Ollama (local)** preset (fills `http://localhost:11434/v1`
   and `qwen2.5vl:7b`; no API key needed).

Needs a GPU with ~8 GB VRAM (or patience on CPU). A 7B model reads the boards well but
can slip on decorative fonts — the local math engine below covers most of it.

### Local math engine (recommended, on by default)

Vision models are great at *reading* but often *miscalculate*. So the helper asks the
model only to transcribe each task, then **recomputes every arithmetic expression in
Python** — 100% accurate, instant, works with any provider (cloud or local). Word
problems and riddles still use the model's answer. Toggle it in setup under **Solving**.

> Pure classic OCR (Tesseract, no AI) was tested and does **not** read this game's
> stylized, skewed font reliably — a vision model is needed for the reading step.

## Use

### Linux

It runs on Linux too. Use the scripts in `linux/`:

```bash
bash linux/setup.sh   # first time: makes a .venv, installs deps, opens the config window
bash linux/run.sh     # launches the helper (re-runs itself with sudo — see below)
```

Global hotkeys need **root** on Linux (the `keyboard` library reads `/dev/input`), so
`run.sh` re-runs itself with `sudo -E` while keeping your X display. Everything else
(screenshot, overlay, API call) is identical to Windows.

### Use

Run **`run.bat`**, tab into the game, and press your hotkey (**End**):

1. an overlay box shows **"📸 Sent to Gemini…"** (the same highlight style used for the answer),
2. the screenshot is sent to the model,
3. the moment the reply arrives it **replaces the box** with the answer, highlighted the same way.

Press the quit hotkey (**F8**) to close.

## Files

```
slap-battles-helper/
├─ setup.bat            first-run installer + opens the setup window
├─ run.bat              created by setup (git-ignored)
└─ app/
   ├─ setup.py          the config window (customtkinter)
   ├─ slap_helper.py    the runtime (hotkey → screenshot → AI → overlay)
   └─ config/
      └─ settings.json  your settings + API key (git-ignored)
```

Re-run `setup.bat` any time to change settings — it remembers what you had.
