---
name: ai-second-brain
description: Walks the user through setting up an AI Second Brain and Karpathy-style living wiki with Obsidian and GitHub Copilot CLI. Use whenever the user mentions building a second brain, the Karpathy wiki, organizing ChatGPT or Claude exports in Obsidian, setting up a living wiki with Copilot CLI, connecting MCP tools to a personal knowledge base, or creating reusable daily planning, ideas, and content prompts.
license: MIT
---

# AI Second Brain Setup for GitHub Copilot CLI

This skill walks the user through a GitHub Copilot CLI version of Charlie Hills's AI Second Brain workflow from the MarTech AI newsletter issue "How to build a second brain with Obsidian and Claude Code." The process turns exports and raw research into a living Obsidian wiki that Copilot CLI can maintain.

The setup has three steps. The user can do all three or pick one - confirm at the start which they want.

1. **AI Brain** - export ChatGPT and Claude history, then organize it in Obsidian as tagged, linked Markdown.
2. **Karpathy Wiki** - set up `raw/` and `wiki/` folders with an `AGENTS.md` instruction file so Copilot CLI can compile a living wiki from raw research.
3. **Living Wiki** - add optional MCP connectors, use Copilot CLI remote control when available, and scaffold reusable prompt templates for `today`, `ideas`, and `create` workflows.

## How to use this skill

The user may be non-technical. Default to plain English, avoid assuming CLI familiarity, and explain what each command does before running it.

Use operating-system-appropriate commands:

- Windows PowerShell paths use backslashes, for example `$HOME\Desktop\Brain`.
- macOS and Linux shell paths use forward slashes, for example `~/Desktop/Brain`.
- GitHub Copilot CLI is launched with `copilot`.

At each step there are things Copilot CLI can do (create folders, download instruction files, inspect exports, scaffold prompt templates) and things only the user can do (request data exports, install Obsidian, grant browser or OS permissions, complete OAuth logins). Be explicit about which is which. Pause and wait when the ball is in the user's court.

If the user says "skip step 1, I've already done it" or "just do step 3", honor that. Confirm what's already in place, then jump in.

## Step 0: Pre-flight check

Before touching anything, confirm what the user has installed. Ask:

> Quick check before we start. Do you have:
> 1. **GitHub Copilot CLI** installed and logged in? You can check by running `copilot --version`.
> 2. **Obsidian** downloaded? It is free from obsidian.md.
> 3. Any **ChatGPT or Claude data exports** already requested? The OpenAI export can take 1-3 days, so we may need to start it now and come back.

If Obsidian is not installed, point them to https://obsidian.md and pause until it is done. If they have not requested the ChatGPT export, kick that off first (instructions in Step 1) so it can process in the background while they do the rest.

Also confirm where they want the brain to live. Default: `Desktop\Brain` on Windows or `~/Desktop/Brain` on macOS/Linux. Use `Brain` unless the user pushes back; a one-word folder name avoids path quoting problems.

## Step 1: Build the AI Brain

The goal is to turn years of ChatGPT and Claude conversations into a tagged, linked, searchable Obsidian vault.

### 1a. Request the data exports (user does this)

This is user-only. Walk them through both:

**ChatGPT** (do this first because it can take up to 3 days):
> Go to chatgpt.com -> click your profile icon -> Settings -> Data Controls -> Export data. OpenAI will email you a download link. Tell me when you have requested it.

**Claude** (usually arrives faster):
> Go to claude.ai -> click your profile icon -> Settings -> Privacy -> Export Data. Tell me when you have the ZIP files downloaded and unzipped.

Wait for the user to confirm. If the ChatGPT export will take days, suggest moving to Step 2 since the Karpathy Wiki does not depend on the exports.

### 1b. Set up the vault (Copilot CLI can do this)

Create the Brain folder.

Windows PowerShell:

```powershell
New-Item -ItemType Directory -Force "$HOME\Desktop\Brain" | Out-Null
```

macOS/Linux:

```bash
mkdir -p ~/Desktop/Brain
```

Then ask the user to move the unzipped ChatGPT and Claude export folders into `Brain`. After they confirm, list the folder and verify both exports are present.

Windows PowerShell:

```powershell
Get-ChildItem "$HOME\Desktop\Brain"
```

macOS/Linux:

```bash
ls ~/Desktop/Brain
```

Then tell them:

> Open Obsidian. Click **Open folder as vault** (not "Create new vault"). Select the `Brain` folder. Tell me once it is open.

### 1c. Organize everything (Copilot CLI does this)

Once the vault is open in Obsidian, the user should launch a fresh Copilot CLI session in the Brain folder.

Windows PowerShell:

```powershell
Set-Location "$HOME\Desktop\Brain"
copilot
```

macOS/Linux:

```bash
cd ~/Desktop/Brain
copilot
```

Once Copilot CLI is running in that folder, use this prompt:

```text
Organize this folder into an Obsidian vault. Convert all my ChatGPT and Claude conversations into individual Markdown files with frontmatter for title, date, tags, category, and source. Tag people, places, recurring themes, projects, and topics. Link related conversations together with Obsidian wikilinks. Preserve the original exports in an archive folder and do not delete source data.
```

When it finishes, tell the user:

> Open Obsidian and open Graph View. Every conversation should now be a tagged, linked Markdown file. Browse a few notes to check that titles, dates, tags, and links look right.

## Step 2: Set up the Karpathy Wiki

The AI Brain organizes the past. The Karpathy Wiki is a living knowledge base that grows every time the user drops in new research. The user puts raw sources in `raw/`, and Copilot CLI compiles structured wiki articles in `wiki/`.

Karpathy's framing is worth quoting to the user: "Obsidian is the IDE. The LLM is the programmer. The wiki is the codebase."

### 2a. Create the structure (Copilot CLI can do this)

In the Brain folder, create the two subfolders and download Karpathy's instruction file as `AGENTS.md`, which Copilot CLI reads automatically.

Windows PowerShell:

```powershell
New-Item -ItemType Directory -Force "$HOME\Desktop\Brain\raw", "$HOME\Desktop\Brain\wiki" | Out-Null
Invoke-WebRequest -Uri "https://gist.githubusercontent.com/karpathy/442a6bf555914893e9891c11519de94f/raw" -OutFile "$HOME\Desktop\Brain\AGENTS.md"
```

macOS/Linux:

```bash
mkdir -p ~/Desktop/Brain/raw ~/Desktop/Brain/wiki
curl -L https://gist.githubusercontent.com/karpathy/442a6bf555914893e9891c11519de94f/raw -o ~/Desktop/Brain/AGENTS.md
```

Verify the download worked.

Windows PowerShell:

```powershell
Get-Item "$HOME\Desktop\Brain\AGENTS.md"
```

macOS/Linux:

```bash
ls -la ~/Desktop/Brain/AGENTS.md
```

If the download fails, fetch the gist in the browser and save its raw contents to `AGENTS.md` in the Brain folder.

### 2b. Get sources into the raw folder (user-led, with options)

Offer two paths.

**Option A: Drag and drop.** Tell them:
> Open the `raw` folder. Drop in 5-10 sources on a single topic to start: PDFs, articles, transcripts, notes, or Markdown files. Tell me when you are done.

**Option B: Pull from a connected tool.** This requires an MCP server or connector that the user has configured for the relevant service. Do not claim a connector exists until it is installed and visible in `/mcp`. If they want this option, help them use `/mcp add` or their tool's documented setup, then pull sources into `raw/`.

### 2c. Build the wiki (Copilot CLI does this)

Once sources are in `raw/`, launch Copilot CLI from the Brain folder and run:

```text
Process every source in the raw folder using the wiki instructions in AGENTS.md. For each source, write a summary page in the wiki folder, create or update relevant topic and concept pages, add Obsidian wikilinks between related pages, and maintain an index file listing every wiki page with a one-line description. Preserve source files and report any files you could not parse.
```

This can take several minutes. When it finishes, tell the user to open Obsidian, inspect `wiki/`, and use Graph View to see the links.

### 2d. Iterate (ongoing)

Coach the user on what to do next:
> The wiki will not be perfect on the first run. Ask Copilot CLI to adjust it: "split this page", "merge these two", "add more detail here", or "check the wiki for contradictions, orphan pages, and missing concept pages."

## Step 3: Give the wiki a heartbeat

This step turns a static wiki into something the user can update and query regularly from Copilot CLI.

### 3a. Optional MCP connectors

Useful sources can include Gmail, calendars, meeting transcripts, notes apps, browser exports, and NotebookLM sources. Use only connectors the user has installed and authorized.

For Copilot CLI:

1. Run `/mcp` to inspect configured servers.
2. Use `/mcp add` for any connector the user wants to configure.
3. Follow the connector's login or OAuth process outside Copilot CLI if required.
4. Test with a small read-only request before importing data into the wiki.

When importing from any connector, save raw copies into `raw/` first, then process them into `wiki/`. Do not overwrite source data.

### 3b. Phone or remote access

The original workflow used Claude Code iMessage Channels. That is not a GitHub Copilot CLI feature.

For Copilot CLI, use `/remote` when available to control or resume the session from GitHub web or mobile. If `/remote` is unavailable in the user's environment, skip this step and keep the workflow terminal-based.

Tell the user:
> Run `/remote` inside Copilot CLI and follow the prompts. Keep the Copilot CLI session running on your computer if you want remote interactions to continue.

### 3c. Scaffold reusable prompt templates

Copilot CLI has built-in slash commands. Do not claim `/today`, `/ideas`, or `/create` are native custom slash commands unless the user's installed CLI or plugin explicitly supports custom commands.

Instead, create reusable prompt templates in the Brain vault, for example:

```text
prompts/today.md
prompts/ideas.md
prompts/create.md
```

Use these starter templates:

**`prompts/today.md` - daily plan**

```markdown
# Today prompt

Review today's calendar notes, urgent email or task inputs I provide, and relevant wiki pages. Give me a prioritized plan for the day with context on people, projects, risks, and next actions.
```

**`prompts/ideas.md` - content ideas**

```markdown
# Ideas prompt

Review recent meeting notes, saved articles, email summaries, and wiki pages. Find patterns and emerging topics. Generate content ideas, topics to write about, and opportunities I may be missing.
```

**`prompts/create.md` - content drafting**

```markdown
# Create prompt

Take the topic I provide, pull relevant context from the wiki, and draft a finished piece of content in my voice. Ask what format I want if I do not specify one: post, newsletter draft, video script, or infographic outline.
```

Tell the user they can invoke a template by mentioning the file in Copilot CLI, for example:

```text
Use @prompts/today.md with the notes in @daily/2026-05-03.md.
```

## Wrapping up

After the selected steps, tell the user what they now have:

- Past AI conversations, tagged and linked in Obsidian.
- A living wiki that grows when they drop new research into `raw/`.
- Optional MCP-connected imports, depending on what they configured.
- Reusable prompt templates for daily planning, ideas, and content creation.

Suggest one next move: drop one new source into `raw/`, then ask Copilot CLI to process it into the wiki and create one output from it.

If they get stuck at any step, debug the specific failure rather than restarting the whole process.

## Things that commonly go wrong

- **Wrong working directory** - Copilot CLI reads from the folder where it was launched. Confirm it is running in the Brain folder before organizing or processing files.
- **Folder name has spaces** - use `Brain`, not `LLM Brain`, to avoid quoting mistakes.
- **Exports are still zipped** - unzip them before asking Copilot CLI to organize them.
- **MCP connector is not installed** - run `/mcp` and configure it before asking Copilot CLI to read external services.
- **Assuming iMessage Channels exist** - they are Claude Code-specific. Use `/remote` in Copilot CLI if available.
- **Gist download fails** - save the raw gist contents manually as `AGENTS.md`.
