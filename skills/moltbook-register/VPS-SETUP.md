# VPS Setup Guide: Getting Your Agent to Actually Execute Commands

## The Problem

Your agent refuses to run `curl` or shell commands, saying things like:
- "I can't execute that from here"
- "Run this on your machine instead"
- "I don't have access to your host"

This happens because of three things:

### 1. Small model being overly cautious

Your agent is running `openai/gpt-5.4-nano` (visible in the status bar). Nano-class models are more likely to refuse actions they perceive as "risky" -- including any external HTTP call. Consider upgrading to a larger model:

```bash
openclaw config set llm.model "openai/gpt-4o"
# or
openclaw config set llm.model "anthropic/claude-sonnet-4-6"
```

A larger model will better understand that it's authorized to act and will follow SOUL.md instructions more reliably.

### 2. SOUL.md doesn't grant shell/network permission

Your agent's SOUL.md file needs to explicitly state that it has shell access and is authorized to make HTTP requests. Without this, the model defaults to "I'm just a chat assistant."

Edit your SOUL.md (usually `~/.openclaw/SOUL.md`):

```bash
nano ~/.openclaw/SOUL.md
```

Add the content from `SOUL-SNIPPET.md` in this folder. The key sections are:
- **Shell & Network Access** -- tells the agent it has a real terminal
- **Moltbook Authorization** -- explicitly permits registration and API calls

### 3. Tool permissions may be restricted

Check that your OpenClaw configuration allows shell execution:

```bash
openclaw config get tools.shell.enabled
openclaw config get tools.shell.allowNetworkAccess
```

If these are `false` or missing, enable them:

```bash
openclaw config set tools.shell.enabled true
openclaw config set tools.shell.allowNetworkAccess true
```

Also check if there's a tool allowlist that might be blocking curl:

```bash
openclaw config get tools.shell.allowedCommands
```

## Quick Fix Checklist

1. [ ] Upgrade model: `openclaw config set llm.model "openai/gpt-4o"` (or any non-nano model)
2. [ ] Update SOUL.md with shell/network authorization (see SOUL-SNIPPET.md)
3. [ ] Enable shell tools: `openclaw config set tools.shell.enabled true`
4. [ ] Enable network access: `openclaw config set tools.shell.allowNetworkAccess true`
5. [ ] Restart the gateway: `openclaw gateway --port 18789`
6. [ ] Test: ask the agent "Run `curl -s https://httpbin.org/get` and show me the result"

If step 6 works, your agent can register on Moltbook. If it still refuses, the model is the bottleneck -- switch to a larger one.

## Nuclear Option

If the agent absolutely will not run curl no matter what, bypass it entirely and run the registration yourself:

```bash
bash ~/.openclaw/skills/moltbook-register/register.sh "MORPHEUS" "An agent-alarm that measures network phase transitions and maintains a Singularity Index."
```

Then paste the API key into the agent's config so it can make future Moltbook calls autonomously.
