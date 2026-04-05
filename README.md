# OpenClaw + Moltbook Training Session

> **CAUTION -- Please Read Before Proceeding**
>
> - **Do NOT install OpenClaw on your work laptop.** Use a personal device or a dedicated cloud VM only. OpenClaw is third-party software that has not been vetted by your employer's security team.
> - **Do NOT share client data, sensitive information, PII, or any confidential material** with OpenClaw. This includes names, emails, financial records, health data, internal documents, or anything subject to NDA or regulatory requirements.
> - **Be extremely careful downloading skills from ClawHub.** Community-published skills are not audited and may contain malware, viruses, phishing attempts, or scams. Only install skills from sources you explicitly trust, and review their code before running them.
> - **Be careful connecting OpenClaw to email, calendars, file storage, or any tool that contains sensitive data.** Once connected, the agent may read, process, or transmit information from those services. Only connect personal or test accounts during this training session.

---

## What We Will Accomplish Today

By the end of this 1-hour session, you will have:

1. **A running OpenClaw AI agent** -- either on a Google Cloud VM or on your personal laptop
2. **Your OpenClaw agent connected to Slack or your messaging device of choice** -- responding to messages in your Slack workspace
3. **Your agent deployed to Moltbook** -- registered, verified, and posting on the "front page of the agent internet"

---

## What You Need to Get Started

Before the session, make sure you have the following ready:

### Accounts & Access

- **Gmail account** -- for Google Cloud authentication and general access
- **Twitter/X account** -- required for Moltbook agent verification
- **Personal Slack workspace (or Whatsapp, Telegram)** -- where you have permission to install apps (admin access or a test workspace)
- **Personal Anthropic API key (or OpenAI, Gemini)** -- sign up at [console.anthropic.com](https://console.anthropic.com) (OpenClaw uses Claude by default)

### Infrastructure (choose one)

- **Option A: Google Cloud Platform** -- a GCP account on [console.cloud.google.com](https://console.cloud.google.com) with billing enabled
- **Option B: Personal laptop** -- macOS, Linux, or Windows (WSL2 recommended)

### Tools

- **Terminal access** -- Terminal (macOS), your preferred terminal emulator (Linux), or WSL2 (Windows)
- **An AI coding assistant** (at least one):
  - [Claude Code](https://docs.anthropic.com/en/docs/claude-code) (recommended)
  - [Cursor](https://cursor.sh)
  - Or another AI coding tool
- **Google Cloud CLI** (Option A only) -- install from [cloud.google.com/sdk/docs/install](https://cloud.google.com/sdk/docs/install)

---

## Step-by-Step Instructions

### Part 1: Setup OpenClaw Agent

Choose **Option A** (Google Cloud) or **Option B** (Personal Laptop) below.

---

#### Option A: Setup on Google Cloud Platform

**1.1 -- Install the Google Cloud CLI (`gcloud`)**

Follow the instructions for your operating system:

- **macOS:** `brew install --cask google-cloud-sdk`
- **Linux (Debian/Ubuntu):**

  ```bash
  sudo apt-get install -y apt-transport-https ca-certificates gnupg curl
  curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo gpg --dearmor -o /usr/share/keyrings/cloud.google.gpg
  echo "deb [signed-by=/usr/share/keyrings/cloud.google.gpg] https://packages.cloud.google.com/apt cloud-sdk main" | sudo tee /etc/apt/sources.list.d/google-cloud-sdk.list
  sudo apt-get update && sudo apt-get install -y google-cloud-cli
  ```

- **Windows (WSL2):** Follow the Linux instructions above inside your WSL2 terminal.
- **Other platforms:** See [cloud.google.com/sdk/docs/install](https://cloud.google.com/sdk/docs/install)

Verify the installation:

```bash
gcloud --version
```

**1.2 -- Authenticate and create a GCP project**

```bash
gcloud init
gcloud auth login
gcloud projects create my-openclaw-project --name="OpenClaw Gateway"
gcloud config set project my-openclaw-project
gcloud services enable compute.googleapis.com
```

**1.3 -- Create a Compute Engine VM**

```bash
gcloud compute instances create openclaw-gateway \
  --zone=us-central1-a \
  --machine-type=e2-small \
  --boot-disk-size=20GB \
  --image-family=debian-12 \
  --image-project=debian-cloud
```

> An `e2-small` instance (~~$12/mo) is the minimum recommended. Use `e2-medium` (~~$25/mo) for a more comfortable experience.

**1.4 -- SSH into your VM**

```bash
gcloud compute ssh openclaw-gateway --zone=us-central1-a
```

**1.5 -- Install Homebrew and Node.js on the VM**

```bash
sudo apt-get update
sudo apt-get install -y build-essential curl git
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

Follow the instructions printed at the end to add Homebrew to your PATH:

```bash
echo >> ~/.bashrc
echo 'eval "$(/home/linuxbrew/.linuxbrew/bin/brew shellenv)"' >> ~/.bashrc
source ~/.bashrc
```

Install Node.js:

```bash
brew install node
```

Verify:

```bash
brew --version
node --version   # should show v22.x.x
npm --version
```

**1.6 -- Install and configure OpenClaw**

```bash
npm install -g openclaw@latest
openclaw onboard --install-daemon
```

The onboarding wizard will walk you through Gateway initialization, workspace setup, and API key configuration. Have your LLM API key ready:

- **Anthropic (Claude):** get your key at [console.anthropic.com](https://console.anthropic.com)
- **OpenAI (GPT):** get your key at [platform.openai.com/api-keys](https://platform.openai.com/api-keys)
- **Google (Gemini):** get your key at [aistudio.google.com/apikey](https://aistudio.google.com/apikey)

**1.7 -- Start the Gateway**

```bash
openclaw gateway --port 18789
```

> To keep the Gateway running after you disconnect from SSH, use `tmux` or `screen`:
>
> ```bash
> sudo apt-get install -y tmux
> tmux new -s openclaw
> openclaw gateway --port 18789
> # Press Ctrl+B then D to detach; reattach later with: tmux attach -t openclaw
> ```

**1.8 -- Access the dashboard via SSH tunnel**

From your local machine (not the VM):

```bash
gcloud compute ssh openclaw-gateway --zone=us-central1-a -- -L 18789:127.0.0.1:18789
```

Open your browser to `http://127.0.0.1:18789/` to see the OpenClaw dashboard.

**1.9 -- Verify it's running**

```bash
openclaw doctor
```

---

#### Option B: Setup on Personal Laptop

**1.1 -- Install Homebrew and Node.js**

If you don't have Homebrew yet (macOS/Linux):

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

Install Node.js 22+:

```bash
brew install node
```

Verify both are working:

```bash
brew --version
node --version   # should show v22.x.x
npm --version
```

> **Windows (WSL2):** Use `nvm` instead -- `curl -fsSL https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash` then `nvm install 22`

**1.2 -- Install OpenClaw**

```bash
npm install -g openclaw@latest
```

**1.3 -- Run the onboarding wizard**

```bash
openclaw onboard --install-daemon
```

This interactive wizard will walk you through Gateway initialization, workspace setup, and API key configuration. Have your LLM API key ready:

- **Anthropic (Claude):** get your key at [console.anthropic.com](https://console.anthropic.com)
- **OpenAI (GPT):** get your key at [platform.openai.com/api-keys](https://platform.openai.com/api-keys)
- **Google (Gemini):** get your key at [aistudio.google.com/apikey](https://aistudio.google.com/apikey)

**1.4 -- Start the Gateway**

```bash
openclaw gateway --port 18789
```

**1.5 -- Access the dashboard**

Open your browser to `http://127.0.0.1:18789/`

**1.6 -- Verify your setup**

```bash
openclaw doctor
```

This command diagnoses any configuration or security issues.

---

### Part 2: Setup OpenClaw Agent on Slack

These steps apply regardless of whether you chose GCP or laptop in Part 1.

**2.1 -- Create a Slack App**

1. Go to [api.slack.com/apps](https://api.slack.com/apps)
2. Click **Create New App** > **From scratch**
3. Name your app (e.g., "OpenClaw Agent") and select your workspace
4. Click **Create App**

**2.2 -- Enable Socket Mode**

1. In the left sidebar, click **Socket Mode**
2. Toggle **Enable Socket Mode** to ON
3. When prompted, create an App-Level Token:
  - Name it `openclaw-socket`
  - Add the scope `connections:write`
  - Click **Generate**
4. Copy the token that starts with `xapp-...` -- you will need this

**2.3 -- Add Bot Token Scopes**

1. In the left sidebar, go to **OAuth & Permissions**
2. Scroll to **Scopes** > **Bot Token Scopes**
3. Add these scopes:

  | Scope               | Purpose                       |
  | ------------------- | ----------------------------- |
  | `chat:write`        | Send messages                 |
  | `channels:history`  | Read channel messages         |
  | `channels:read`     | List channels                 |
  | `groups:history`    | Read private channel messages |
  | `im:history`        | Read DM messages              |
  | `im:read`           | View DM info                  |
  | `im:write`          | Open DMs                      |
  | `mpim:history`      | Read group DMs                |
  | `mpim:read`         | View group DM info            |
  | `mpim:write`        | Open group DMs                |
  | `users:read`        | List users                    |
  | `app_mentions:read` | Detect @mentions              |
  | `reactions:read`    | Read reactions                |
  | `reactions:write`   | Add reactions                 |
  | `files:read`        | Read files                    |
  | `files:write`       | Upload files                  |


**2.4 -- Subscribe to Bot Events**

1. In the left sidebar, go to **Event Subscriptions**
2. Toggle **Enable Events** to ON
3. Under **Subscribe to bot events**, add:
  - `app_mention`
  - `message.channels`
  - `message.groups`
  - `message.im`
  - `message.mpim`
  - `reaction_added`
  - `reaction_removed`

**2.5 -- Enable App Home**

1. In the left sidebar, go to **App Home**
2. Under **Show Tabs**, enable the **Messages Tab**
3. Check **Allow users to send Slash commands and messages from the messages tab**

**2.6 -- Install the App to Your Workspace**

1. Go to **OAuth & Permissions**
2. Click **Install to Workspace** and authorize
3. Copy the **Bot User OAuth Token** that starts with `xoxb-...`

**2.7 -- Connect Slack to OpenClaw**

Run the channel login command and follow the prompts:

```bash
openclaw channels login
```

When prompted, select **Slack** and enter:

- Your **App Token** (`xapp-...` from step 2.2)
- Your **Bot Token** (`xoxb-...` from step 2.6)

**2.8 -- Restart the Gateway**

```bash
# Stop the running gateway (Ctrl+C) and restart
openclaw gateway --port 18789
```

> **GCP users with tmux:** Reattach with `tmux attach -t openclaw`, stop the gateway, reconfigure, and restart.

**2.9 -- Test the connection**

1. In Slack, invite the bot to a channel: type `/invite @OpenClaw Agent` (or whatever you named it)
2. Mention the bot: `@OpenClaw Agent hello!`
3. You should receive a response from your agent

**2.10 -- Verify channel status**

```bash
openclaw channels status --probe
```

---

### Part 3: Deploy OpenClaw Agent to Moltbook

**3.1 -- Read the Moltbook skill file**

Open the Moltbook skill instructions in your browser:

> [https://www.moltbook.com/skill.md](https://www.moltbook.com/skill.md)

Follow the instructions there to join Moltbook. The process has three steps:

1. **Run the command** from the skill file to get started
2. **Register your agent & send your human the claim link** -- your agent will register on Moltbook and generate a claim URL for you (the human owner) to confirm
3. **Once claimed, start posting!** -- your agent is live on Moltbook

**3.2 -- Verify via Twitter/X**

Moltbook requires a tweet to verify ownership. Post the `verification_code` your agent received during registration. Without this step, your agent's posts will not appear on Moltbook.

**3.3 -- Confirm your agent is live**

Once claimed and verified, your agent will automatically visit Moltbook every 4 hours via the Heartbeat system -- browsing, posting, and commenting without human intervention. Make sure your Gateway is running continuously (GCP) or that the daemon is installed (laptop).

---

## Troubleshooting


| Problem                           | Solution                                                                       |
| --------------------------------- | ------------------------------------------------------------------------------ |
| `node --version` shows < 22       | Install Node 22+ from [nodejs.org](https://nodejs.org) or use `nvm install 22` |
| GCP VM runs out of memory         | Upgrade to `e2-small` or `e2-medium`                                           |
| Slack bot not responding          | Check `openclaw channels status --probe` and verify tokens                     |
| Moltbook posts not appearing      | Ensure Twitter verification is complete                                        |
| Gateway won't start               | Run `openclaw doctor` to diagnose issues                                       |
| `npm install -g` permission error | Use `sudo npm install -g openclaw@latest` or fix npm permissions with `nvm`    |


## Resources

- [OpenClaw Documentation](https://docs.openclaw.ai)
- [OpenClaw GitHub](https://github.com/openclaw/openclaw)
- [OpenClaw GCP Deployment Guide](https://docs.openclaw.ai/install/gcp)
- [OpenClaw Slack Channel Setup](https://docs.openclaw.ai/channels/slack)
- [Moltbook Developer Docs](https://www.moltbook.com/developers)
- [Moltbook API GitHub](https://github.com/moltbook/api)

