# Start with Pi: the detailed guide

This is the long version of the [README](README.md). It takes about 15 minutes, and most of that is waiting for installers.

- [0. Why an agent on your computer?](#0-why-an-agent-on-your-computer)
- [1. Install Pi](#1-install-pi)
- [2. Get an OpenRouter key](#2-get-an-openrouter-key)
- [3. Log in from Pi](#3-log-in-from-pi)
- [4. Choose a model](#4-choose-a-model)
- [5. Test that it works](#5-test-that-it-works)
- [6. Recommended: cheap, fast, private routing](#6-recommended-cheap-fast-private-routing)
- [7. Let Pi set up other apps (ChatGPT / Codex)](#7-let-pi-set-up-other-apps-chatgpt--codex)
- [8. Give your agent more hands: CLI tools](#8-give-your-agent-more-hands-cli-tools)
- [9. Which model should I use?](#9-which-model-should-i-use)
- [10. Other things worth trying](#10-other-things-worth-trying)
- [Troubleshooting](#troubleshooting)

---

## 0. Why an agent on your computer?

| | AI in a browser (ChatGPT, Claude.ai…) | AI in a harness on your computer (Pi) |
|---|---|---|
| Sees | What you paste or upload | Your files and folders, when you point it at them |
| Does | Talks, searches the web, runs a sandbox | Runs real commands, edits files, installs and configures software |
| Tools | Whatever the website ships | Whatever you add, and Pi can build new tools for itself |
| Result | Gives you instructions to follow | Does the steps itself and shows you each one |

The **model** (GLM, Claude, GPT…) is the brain. The **harness** (Pi) is the body: it gives the model tools such as `read`, `write`, `edit` and `bash`, plus **skills**, which are instruction packs it loads when needed. Put a good brain in a body and a lot more becomes possible. Instead of "here are 12 terminal commands, good luck", you say "set this up for me" and watch it happen.

Pi is deliberately small and open. You can change almost anything about it, often just by asking Pi. That makes it a good **safe home base**: if a fancier tool breaks, open Pi and ask it to fix it.

> ⚠️ An agent really runs commands on your machine. Pi shows every command and edit as it happens, but it does **not** ask before each one. Start in a test folder, keep backups of important work, and read what it's doing.

---

## 1. Install Pi

Go to **[pi.dev](https://pi.dev)**. The page shows the install command for your system with a copy button.

### Mac (or Linux)

1. Open **Terminal** (`Cmd+Space`, type "Terminal", press Enter).
2. Paste and press Enter:
   ```bash
   curl -fsSL https://pi.dev/install.sh | sh
   ```
3. If you don't have Node.js, the installer offers to install it (through Homebrew if you have it). It may ask for your Mac password. When you type a password in Terminal, nothing appears on screen. That's normal.

### Windows

1. Open the Start menu, type **PowerShell**, right-click it and choose **Run as administrator**.
   (The installer works without admin, but with admin it can install shared helpers like **Git for Windows** through `winget` without permission problems.)
2. Paste and press Enter:
   ```powershell
   powershell -c "irm https://pi.dev/install.ps1 | iex"
   ```
3. Answer the questions:
   - **Node.js:** say yes. Pi needs Node 22.19 or newer, and the installer downloads a private copy.
   - **Git Bash:** choose the default (portable Git) or `w` (install Git for Windows with winget). Pi runs its commands through Git Bash.
4. For the best experience use **[Windows Terminal](https://aka.ms/terminal)**, which comes with Windows 11. The old blue PowerShell window has display bugs.

### Check it

Close the terminal, open a **new** one, and run:

```bash
pi --version
```

If you see a version number, Pi is installed. Later, update it with `pi update`.

---

## 2. Get an OpenRouter key

[OpenRouter](https://openrouter.ai) is one account that gives you access to hundreds of models (GLM, DeepSeek, Claude, GPT, Gemini…) from many providers. You pay per use, often cents per session with cheap models.

1. Sign up or log in at **[openrouter.ai](https://openrouter.ai)**.
2. Add credit: **[openrouter.ai/credits](https://openrouter.ai/credits)**. $5–10 lasts a long time with GLM Flash.
3. Create a key: **[openrouter.ai/keys](https://openrouter.ai/keys)** → **Create key**.
   - Give it a name like `pi-laptop`.
   - Optional but smart: set a **credit limit** on the key, such as $5.
4. Copy the key. It starts with `sk-or-…`, and OpenRouter shows it only once.

> 🔐 Treat the key like a password. Don't paste it into chats, screenshots or shared files.

---

## 3. Log in from Pi

Go to a folder you want Pi to work in, then start Pi:

```bash
mkdir -p ~/pi-playground && cd ~/pi-playground
pi
```

Inside Pi, type:

```text
/login openrouter
```

(or just `/login` and pick **OpenRouter** from the list), then paste your key. Pi saves it in `~/.pi/agent/auth.json`.

Once you log in, Pi asks OpenRouter which models your key can use and lists only those.

---

## 4. Choose a model

```text
/model
```

Type `glm-5.3-flash`, pick **openrouter / z-ai/glm-5.3-flash**, and press Enter.

- `Ctrl+S` in the picker saves it as your default for new sessions.
- `Ctrl+L` opens the picker any time. `Ctrl+P` cycles through your favourite models.
- Newer version out by the time you read this? Search `glm` and choose the newest **flash** model, or use the alias `~z-ai/glm-flash-latest`, which always points to the latest one.

Why GLM Flash? It's **cheap** (about $0.15 per million input tokens), **fast**, and **good enough** to get a real feel for what an agent can do.

---

## 5. Test that it works

Try a prompt that makes Pi **use your computer**, not just chat:

```text
What computer am I on? Check the OS version, CPU and free disk space, then say hi.
```

You should see Pi run one or two commands (shown in the chat) and then answer. That's the moment it clicks: the model just looked at your actual machine.

A few more to try:

```text
Create a file called todo.md with three things I could try with an AI agent.
```
```text
What's taking up the most space in my Downloads folder? Don't delete anything.
```
```text
Explain what you just did, command by command, like I'm new to the terminal.
```

---

## 6. Recommended: cheap, fast, private routing

Many different companies (providers) host the same open model on OpenRouter, each with its own price, speed and **data policy**. Some keep or log your prompts. By default OpenRouter picks mainly on price.

This override tells OpenRouter what you care about:

```json
{
  "providers": {
    "openrouter": {
      "modelOverrides": {
        "z-ai/glm-5.3-flash": {
          "compat": {
            "openRouterRouting": {
              "zdr": true,
              "max_price": { "prompt": 0.15, "completion": 0.5 },
              "preferred_max_latency": { "p50": 2 },
              "sort": "throughput"
            }
          }
        }
      }
    }
  }
}
```

| Setting | Type | What it does |
|---|---|---|
| `zdr: true` | **Hard rule** | Only providers with **Zero Data Retention**: they don't store your prompts after answering. |
| `max_price` | **Hard rule** | Skips providers charging more than $0.15 per million input tokens or $0.50 per million output tokens. |
| `preferred_max_latency.p50: 2` | Soft preference | Gives lower priority to providers that usually take more than 2 seconds to start answering. They aren't excluded. |
| `sort: "throughput"` | Ordering | Out of what's left, uses the **fastest** provider first. |

When I checked (November 2026), more than 20 providers met all of these rules for GLM 5.3 Flash, so you get privacy without losing much choice.

### Install it (let Pi do it)

This is the first "agent does it for you" moment. In Pi, paste:

```text
Merge the OpenRouter routing override from
https://github.com/jfanals/start-with-pi/blob/main/config/pi-models.json
into my ~/.pi/agent/models.json. Create the file if it doesn't exist and keep anything
already in it. Show me the final file, then run /reload.
```

### Or do it by hand

Open `~/.pi/agent/models.json` (on Windows `%USERPROFILE%\.pi\agent\models.json`), paste the JSON above (or merge it in if the file exists), save, and run `/reload` in Pi.

### Check it

Send a prompt, then open [openrouter.ai/activity](https://openrouter.ai/activity). Each request shows which provider served it.

> 💡 You can also turn on ZDR for **every** request in your account under [OpenRouter → Settings → Privacy](https://openrouter.ai/settings/privacy). That applies to all your apps, not only Pi.

---

## 7. Let Pi set up other apps (ChatGPT / Codex)

Not everyone wants to live in a terminal. The **ChatGPT desktop app** includes **Codex**, OpenAI's coding agent, with a friendlier window. Codex can also use OpenRouter, which means the same cheap GLM model.

Setting this up by hand takes a few tricky steps: a hidden config file, and an environment variable that desktop apps can see. That's a good job to hand to Pi.

1. Install the ChatGPT desktop app from **[chatgpt.com/download](https://chatgpt.com/download)** and sign in once.
2. In Pi, paste:
   ```text
   Set up the ChatGPT desktop app (Codex) to use OpenRouter with z-ai/glm-5.3-flash.
   Follow https://openrouter.ai/docs/cookbook/coding-agents/codex-desktop and use
   https://github.com/jfanals/start-with-pi/blob/main/config/codex-config.toml as the config.
   Back up ~/.codex/config.toml first if it exists.
   Make OPENROUTER_API_KEY visible to desktop apps (launchctl on Mac, setx on Windows).
   Don't print my key in the chat. Ask me to paste it when you need it.
   ```
3. **Fully quit** ChatGPT (not just close the window) and reopen it. Choose **Codex** and start a task.

What Pi does under the hood is in [config/codex-config.toml](config/codex-config.toml):

- writes `~/.codex/config.toml` with an `openrouter` provider and `model = "z-ai/glm-5.3-flash"`;
- **Mac:** `launchctl setenv OPENROUTER_API_KEY "sk-or-…"` (this lasts until you log out; Pi can add a login item to make it permanent);
- **Windows:** `setx OPENROUTER_API_KEY "sk-or-…"` (permanent).

> Known rough edges (late 2026): custom providers in the desktop app may not appear in the model picker, and old chats may not mix well with the new provider ([openai/codex#29156](https://github.com/openai/codex/issues/29156)). If the app keeps using old settings, quit it and end any leftover `gpt`/`codex` processes in Activity Monitor or Task Manager. If something breaks, go back to Pi and ask it to fix it. That's the whole idea.

---

## 8. Give your agent more hands: CLI tools

This is the most important idea in the guide:

> **Surround yourself with tools the agent can drive.** Anything with a command-line tool (CLI) or an MCP server, the agent can use for you. Add enough of them and almost anything you can do on your computer, the agent can do too.

Two kinds of tools:

- **CLI**: a terminal command such as `gh`, `wrangler` or `agent-browser`. Pi already has `bash`, so a newly installed CLI is immediately usable. Just ask.
- **MCP** (Model Context Protocol): a standard way for apps to offer tools to agents. Pi supports it built in: `pi mcp add …`, then `/mcp` to manage it.

You rarely need to learn these tools yourself. Install one, log in once, and then just describe what you want.

### GitHub: share code, publish websites

A free [GitHub](https://github.com) account is your agent's home on the internet:

- **Save and share** what you and your agent build (like this guide). Every change is versioned, so you can always go back.
- **Free websites** with [GitHub Pages](https://docs.github.com/en/pages): turn a folder of HTML into a site at `https://<you>.github.io/<project>`.
- **Learn from others:** point Pi at any public repo and ask it to explain, install or adapt it.

Setup: create an account at [github.com](https://github.com), then ask Pi:

```text
Install the GitHub CLI (gh) and log me in with `gh auth login` using the browser.
```

Then try:

```text
Make a one-page website about my hobby in a new folder, push it to a new public GitHub
repo, and publish it with GitHub Pages. Give me the link.
```

### Cloudflare: domains, DNS and free hosting

[Cloudflare](https://www.cloudflare.com) is excellent for everything around websites:

- **Domains and DNS:** buy domains at cost with [Cloudflare Registrar](https://www.cloudflare.com/products/registrar/) and manage DNS in one place.
- **Free hosting:** deploy sites to [Workers](https://developers.cloudflare.com/workers/static-assets/get-started/). Every account gets a free `*.workers.dev` subdomain. On the free plan, requests for static files (HTML, CSS, images) are free and unlimited. Code that runs on each request gets 100,000 requests a day.
- **[Wrangler](https://developers.cloudflare.com/workers/wrangler/)**, Cloudflare's CLI, deploys with a single command. Cloudflare also runs an [MCP server](https://github.com/cloudflare/mcp) (`https://mcp.cloudflare.com/mcp`) covering its whole API.

Setup: create a free account at [cloudflare.com](https://dash.cloudflare.com/sign-up), then ask Pi:

```text
Deploy the website in this folder to Cloudflare Workers as a static site using wrangler.
Log me in with `npx wrangler login` first. Give me the workers.dev link.
```

Later: *"Connect my domain example.com to that site and set up the DNS."*

### Beyond the terminal: browser and desktop control

With these, the agent can leave the terminal and use your computer the way you do.

- **[agent-browser](https://github.com/vercel-labs/agent-browser)** (Vercel Labs) lets the agent drive a real Chrome: open pages, read them, click, fill forms and take screenshots. It's useful for sites that have no CLI or API.
  ```bash
  npm install -g agent-browser && agent-browser install
  ```
- **[agent-desktop](https://github.com/vercel-labs/agent-desktop)** (Vercel Labs) gives the agent **computer use**: it can see and operate desktop apps (Finder, Settings, any app) on macOS, Windows and Linux. It reads the system's accessibility tree, the same information screen readers use, so it doesn't rely on screenshots.
  ```bash
  npm install -g agent-desktop
  agent-desktop permissions --guide   # macOS: grants Accessibility access
  ```

Both come with a **skill** that teaches the agent how to use them. Easiest is to ask Pi:

```text
Install agent-browser and agent-desktop from vercel-labs on GitHub, including their skills,
and walk me through any permissions I need to grant.
```

Then try: *"Open my calendar app and tell me what's on tomorrow"* or *"Go to the OpenRouter activity page and tell me how much I spent this week."*

> ⚠️ Desktop and browser control is powerful. The agent can see whatever is on screen and click anything. Keep it to tasks you're watching, and don't leave it unattended with banking or email open.

---

## 9. Which model should I use?

| Model | Feel | When |
|---|---|---|
| **GLM 5.3 Flash** (OpenRouter) | Cheap, fast, decent. Needs more guidance. You may need a second or third try and should check its work. | Learning, everyday small tasks, trying ideas without worrying about cost |
| **Claude Opus 5.5** (Anthropic) | Excellent "autopilot": understands what you meant, plans well, finishes long tasks | My daily driver for serious work |
| **GPT 6.1 Sol** (OpenAI) | Also excellent, with a slightly different style | A strong alternative. Worth switching to when one model gets stuck |

My advice: **start with GLM Flash** to learn what agents can do for almost no money. When you're ready to hand off bigger jobs, try a frontier model. In Pi it's one `/model` away. Claude and ChatGPT subscriptions also work with `/login`.

Further on, you can set up **coordinators** that send different tasks to different models, for example a cheap model for searching and a strong one for planning. That's beyond this guide, but Pi can do it.

---

## 10. Other things worth trying

**Other agents and harnesses** (Pi can install them for you; just ask):

- **[Hermes Agent](https://hermes-agent.nousresearch.com)** by Nous Research: a self-improving personal agent with a desktop app.
- **[OpenClaw](https://openclaw.ai)**: a personal AI assistant you can chat with from messaging apps. It's built on top of Pi.

**Voice tools that pair well with agents:**

- **[FluidVoice](https://fluidvoice.com)** ([GitHub](https://github.com/altic-dev/FluidVoice)): free, open-source dictation that runs on your device. It uses NVIDIA's **Parakeet** models (Parakeet TDT v3 handles about 25 languages). Press a hotkey, talk, and the text appears in whatever app has focus, including Pi. Talking to your agent is much faster than typing. (Mac; Windows in beta. Download only from fluidvoice.com or GitHub. fluidvoice.org is not official.)
- **[Kokoro](https://github.com/hexgrad/kokoro)**: a tiny (82M) open text-to-speech model that runs locally and is fast. Good for having the agent read summaries aloud or for voiceovers on videos. Ask Pi: *"Install Kokoro TTS and read this file aloud."*

**Getting more out of Pi:**

- Put an `AGENTS.md` file in a folder with instructions Pi should always follow there.
- `pi install npm:<package>` adds community extensions and skills. Browse [pi.dev/packages](https://pi.dev/packages).
- Missing a feature? Ask Pi to build it as an extension, then `/reload`.

---

## Troubleshooting

| Problem | Fix |
|---|---|
| `pi: command not found` | Open a **new** terminal window. Still missing? Run the installer again. |
| Windows: Pi says it can't find Bash | Run the installer again and choose to install Git Bash, or install [Git for Windows](https://git-scm.com/download/win). |
| Odd characters or crashes on Windows | Use [Windows Terminal](https://aka.ms/terminal) instead of the classic console. |
| `/model` doesn't list GLM | Check `/login openrouter` worked and the key has credit. Run `pi update --models`. |
| "No endpoints found" after step 6 | The rules are too strict for that model today. Raise `max_price`, or remove `zdr` to test. |
| `402` / insufficient credits | Add credit at [openrouter.ai/credits](https://openrouter.ai/credits), or raise the key's limit. |
| ChatGPT/Codex ignores the new config | Quit it fully, end leftover `gpt`/`codex` processes, and reopen. On Mac, run `launchctl setenv` again after logging out. |

When in doubt, describe the problem to Pi. Fixing setup problems like these is what it's for.
