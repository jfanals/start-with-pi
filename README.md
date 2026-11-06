# Start with Pi

A short path from "AI in a browser tab" to "AI that can work on your computer".

A chatbot on the web can talk, search, and use the few tools its website gives it. A **coding agent** runs on your machine. It can read and write files, run commands, install software, and configure other apps. The model brings the intelligence. The **harness** gives it hands: tools, skills, and access to your computer.

[Pi](https://pi.dev) is a small, open harness. I use it as my toolbox, and also as the thing I fall back on when something else breaks. Once Pi works, it can help you set up everything else.

> **New here?** Follow the TL;DR. Want more detail? Read [GUIDE.md](GUIDE.md).

## TL;DR

1. **Install Pi.** Go to [pi.dev](https://pi.dev), click the copy button for your system, and paste the command:
   - **Mac:** paste into **Terminal**.
   - **Windows:** paste into **PowerShell**. Right-click it and choose **Run as administrator** so it can also install helpers like Git.

   ```bash
   # Mac / Linux
   curl -fsSL https://pi.dev/install.sh | sh
   ```
   ```powershell
   # Windows
   powershell -c "irm https://pi.dev/install.ps1 | iex"
   ```
   Say yes when it offers to install Node.js or Git Bash.

2. **Get an OpenRouter key.** Sign in at [openrouter.ai](https://openrouter.ai), add a few dollars of credit, then go to [openrouter.ai/keys](https://openrouter.ai/keys) → **Create key**. Copy it (it starts with `sk-or-`).

3. **Connect Pi.** Open a new terminal and run `pi`. Inside Pi:
   ```text
   /login openrouter
   ```
   Paste your key.

4. **Choose a model.** Type `/model`, search for `glm-5.3-flash`, and press Enter. (`Ctrl+S` saves it as your default.)

5. **Test it.**
   ```text
   What computer am I on? Check the OS version and free disk space, then say hi.
   ```
   If Pi runs a command and answers, it works. Your harness now has intelligence.

6. **Recommended: cheap, fast, private routing.** Paste this into Pi:
   ```text
   Add the OpenRouter routing override from
   https://github.com/jfanals/start-with-pi/blob/main/config/pi-models.json
   to my ~/.pi/agent/models.json without removing anything already there. Then run /reload.
   ```
   With this, Pi only uses providers that keep **zero data** (ZDR), skips expensive ones, and picks the fastest of the rest.

7. **Let Pi set up the rest.** For example, the ChatGPT desktop app with the same cheap model. See [GUIDE.md → step 7](GUIDE.md#7-let-pi-set-up-other-apps-chatgpt--codex).

8. **Give it more hands.** Surround yourself with tools the agent can drive from the command line (CLI) or through MCP. Then almost anything you can do on your computer, it can do too. Ask Pi to install and log in to:
   - **[GitHub](https://github.com)** (`gh`): save and share code, and publish free websites with GitHub Pages.
   - **[Cloudflare](https://www.cloudflare.com)** (`wrangler`): domains, DNS, and free hosting on `*.workers.dev`.
   - **[agent-browser](https://github.com/vercel-labs/agent-browser)** and **[agent-desktop](https://github.com/vercel-labs/agent-desktop)**: the agent drives your browser and desktop apps.

   See [GUIDE.md → step 8](GUIDE.md#8-give-your-agent-more-hands-cli-tools).

## What's in here

| File | What it is |
|---|---|
| [GUIDE.md](GUIDE.md) | The detailed walkthrough, with the reasons behind each step |
| [config/pi-models.json](config/pi-models.json) | The routing override for GLM 5.3 Flash |
| [config/codex-config.toml](config/codex-config.toml) | Settings for using OpenRouter in the ChatGPT desktop app (Codex) |

## License

MIT. Copy it, share it, adapt it.
