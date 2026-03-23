# Vibe Coding: Build AI Apps Without Writing Code

A step-by-step guide to building and deploying AI-powered applications using V0, Claude Code, GitHub, and Vercel.

> It's simpler than it looks.

---

## Services You Need (Create Accounts)

| Service | Purpose |
|---------|---------|
| [v0.app](https://v0.app/) | Develop the prototype and host the demo |
| [GitHub](https://github.com/) | Store all code |
| Claude Code | Build the full app |

You will also need a **Terminal** app on your laptop (Terminal on Mac, or any terminal emulator).

---

## Laptop Setup

Open your Terminal and run these commands:

```bash
# 1. Install Homebrew (Mac package manager)
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# 2. Install Node.js and npm
brew install node

# 3. Install Vercel CLI
brew install vercel-cli
```

---

## The Process

### Phase 1: Research & Spec

1. **Create a project folder.** Keep a `dev` folder where all your coding projects live.
   ```
   e.g. dev/deviation-mgmt
   ```

2. **Gather reference materials.** Store any relevant PDFs, documents, or reference files in the folder.

3. **Open Claude Code inside the folder.**
   ```bash
   cd dev/deviation-mgmt
   claude
   ```

4. **Ask Claude to generate your project spec.** For example:
   > "Read all the files in this folder to learn about deviation management and design a new agentic AI product to improve existing processes. Please think about the agents and the user interface. Generate the CLAUDE.md and SPEC.md files."

   This creates two key files:
   - **CLAUDE.md** — Instructions for Claude Code on how to work with the project
   - **SPEC.md** — Full product specification

---

### Phase 2: Prototype with V0

5. **Go to [v0.app](https://v0.app/)** and upload the `SPEC.md` file. Ask it to create a landing page:
   > "Create a landing page for this SPEC.md"

   It will generate a working landing page in a few minutes.

6. **Connect V0 to GitHub.** In V0, click **Settings** (top right) > **GitHub** > **Connect to a repository**. It will suggest a new repo name (e.g. `v0-deviation-mgmt`). Use the suggested name and create the repo.

7. **Verify the code is in GitHub.** Go to your GitHub repo and confirm Vercel uploaded the code automatically.

---

### Phase 3: Set Up Local Development

8. **Copy the repo URL.** In your GitHub repo, click the green **Code** button and copy the HTTPS URL:
   ```
   https://github.com/<your-user>/v0-deviation-mgmt
   ```

9. **Clone the repo locally.** In your terminal, navigate to your `dev` folder and clone:
   ```bash
   cd dev
   git clone https://github.com/<your-user>/v0-deviation-mgmt
   ```

10. **Move into the new folder.**
    ```bash
    cd v0-deviation-mgmt
    ```

11. **Copy your spec files.** Copy the `CLAUDE.md` and `SPEC.md` files into this new folder (you can use Finder or any file explorer).

12. **Link to Vercel.** Run these commands and follow the prompts:
    ```bash
    vercel login
    vercel link
    ```

---

### Phase 4: Build the Full App with Claude Code

13. **Open Claude Code in the project folder.**
    ```bash
    claude
    ```

14. **Ask Claude Code to build the app.** For example:
    > "Read CLAUDE.md and SPEC.md and read the code in this folder, and complete building the application including front end, data model, sample data, and AI Agents connected with Anthropic Claude models."

15. **Set up environment variables.** Claude may ask you to create a `.env` file with API keys needed for the LLMs to work. Follow its instructions.

16. **Iterate and improve.** From here, just test the app and ask Claude to make improvements as you go.

---

## Ongoing Workflow: Save & Deploy

Every time you improve the app, always do these two things:

### 1. Commit and Push to GitHub

Ask Claude Code:
> "Commit and push"

This saves your code and syncs it to GitHub so nothing is lost.

### 2. Deploy to Vercel

Ask Claude Code:
> "Run vercel link and vercel deploy --prod"

This deploys your latest version to Vercel, where your live demo is hosted.

---

## Summary

```
Gather docs → Claude generates spec → V0 builds prototype → Clone repo locally
→ Claude Code builds full app → Test & iterate → Commit, push, deploy → Repeat
```

That's it. No traditional coding required.
