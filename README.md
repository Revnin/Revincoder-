# Revincoder

Queue several coding jobs. Revincoder asks Google Gemini to write each project and streams the code into
VS Code as human-like typing: it creates the files, types them out with mistakes and corrections, clicks back
up to fix things, takes breaks, and moves on to the next job by itself.

---

## Install on another computer

Pick **one** route:

| Route | Use it when | Tools you need |
|---|---|---|
| **A. Ready-made `.vsix`** | You have `revincoder-0.6.1.vsix` | Only VS Code |
| **B. From the zip (build it yourself)** | You have the zip with the source code | VS Code + Node.js (npm comes with it) |
| **C. From a Git repository** | The project is on GitHub or similar | VS Code + Node.js + Git |

You will also need a **Gemini API key** (free at https://aistudio.google.com/apikey: sign in, click "Create API key")
and an internet connection while it types. The `.vsix` contains no API key, so it is safe to share.

### Step 0: install the tools (only what your route needs)

Check what you already have. Open a terminal (Windows: PowerShell, macOS: Terminal, Linux: any shell):
```
code --version
node -v
npm -v
git --version
```
Any line that says "not recognized" / "command not found" means that tool is missing. Install it below, then **close and reopen the
terminal** and run the check again.

**VS Code** (1.85 or newer): https://code.visualstudio.com/download

**Node.js 18 or newer (includes npm)**: needed for routes B and C only
- Windows: download the **LTS** installer from https://nodejs.org and keep the defaults (this adds `node` and `npm` to PATH).
  Or in PowerShell: `winget install OpenJS.NodeJS.LTS`
- macOS: installer from https://nodejs.org, or `brew install node`
- Linux: `sudo apt install -y nodejs npm` (Debian/Ubuntu) or `sudo dnf install -y nodejs npm` (Fedora).
  If `node -v` shows older than 18, install a newer one from https://nodejs.org (or with `nvm`).

**Git**: needed for route C only (it is **not** needed for the zip route)
- Windows: https://git-scm.com/download/win (defaults are fine), or `winget install Git.Git`
- macOS: `xcode-select --install` or `brew install git`
- Linux: `sudo apt install -y git` or `sudo dnf install -y git`

**The `code` command** (used in the install steps below; skip it if you prefer the "Install from VSIX" button)
- Windows: it is added by the VS Code installer. If missing, reinstall VS Code with "Add to PATH" ticked.
- macOS: open VS Code, `Cmd+Shift+P`, run **Shell Command: Install 'code' command in PATH**.
- Linux: normally added by the VS Code package.

### Route A: ready-made `.vsix` (easiest)

1. Copy `revincoder-0.6.1.vsix` to the other computer.
2. Install it, either:
   - **In VS Code:** Extensions panel (`Ctrl+Shift+X`), click the **`...`** menu, **Install from VSIX...**, pick the file. Or
   - **In a terminal:**
     ```powershell
     code --install-extension "$HOME\Downloads\revincoder-0.6.1.vsix"
     ```
     (macOS/Linux: `code --install-extension ~/Downloads/revincoder-0.6.1.vsix`)
3. Go to **Finish and check** below.

You never unzip the `.vsix`. VS Code installs it as it is.

### Route B: from the zip (extract, build, install)

This is what you do when you only have the zip with the source code (`revincoder.zip` or `revincoder-install.zip`).

1. **Extract the zip.**
   - Windows: right-click the zip, **Extract All...**, or in PowerShell:
     ```powershell
     cd $HOME\Downloads
     Expand-Archive .\revincoder-install.zip -DestinationPath . -Force
     ```
     (for the source-only `revincoder.zip`, use that file name instead)
   - macOS / Linux: `cd ~/Downloads && unzip revincoder-install.zip` (Linux may need `sudo apt install unzip`)
2. **Go into the folder that contains `package.json` and `extension.js`.**
   - From `revincoder-install.zip` that is `revincoder-install\source`
   - From `revincoder.zip` that is `revincoder`
   ```powershell
   cd .\revincoder-install\source
   dir
   ```
   `dir` (or `ls` on Mac/Linux) must list `package.json` and `extension.js`. If it does not, you are in the wrong folder
   (running the next command there gives "Extension manifest not found").
3. **Build the `.vsix`.** There is nothing to `npm install`; `npx` downloads the packager the first time, so you need internet:
   ```powershell
   npx @vscode/vsce package --allow-missing-repository --skip-license
   ```
   It ends with `Packaged: ...revincoder-0.6.1.vsix`.
4. **Install it:**
   ```powershell
   code --install-extension .\revincoder-0.6.1.vsix
   ```
5. Go to **Finish and check** below.

### Route C: from a Git repository

If you put the project in a Git repository (commit `extension.js`, `package.json`, `README.md`, `example.plan.json`):
```bash
git clone <your-repository-url> revincoder
cd revincoder
npx @vscode/vsce package --allow-missing-repository --skip-license
code --install-extension ./revincoder-0.6.1.vsix
```
To update later: `git pull`, build again, install again.

### Finish and check

1. Close **every** VS Code window, then open VS Code again. A window that was open during the install will not show the new commands.
2. Check it is installed:
   ```
   code --list-extensions --show-versions
   ```
   You should see `revin.revincoder@0.6.1`. In VS Code press `Ctrl+Shift+P` and type `Revincoder`: the commands should appear.
3. **First run:**
   1. **File, Open Folder**: open an empty folder for the project.
   2. `Ctrl+Shift+P`, **Revincoder: Add jobs to queue**. Type what to build (e.g. *"a Node.js todo API with Express"*), press Enter,
      add more jobs if you like, then press Enter on an empty box to finish.
   3. Click **Run queue now** (or run **Revincoder: Run queue**).
   4. Paste your Gemini API key when asked. It is kept in VS Code's secret storage on that computer only.

Each queued job goes into its own folder (`01-...`, `02-...`).

### Updating or removing
- **Update:** install the newer `.vsix` the same way (or rebuild from the new source). It replaces the old version.
- **Remove:** `code --uninstall-extension revin.revincoder` (or Extensions panel, gear icon, Uninstall).
- **If you had the older "AutoCoder" version:** `code --uninstall-extension revin.autocoder`

### Moving your settings to the new computer
- Your **API key**, **queued jobs** and **saved progress** stay on the computer where they were created. On the new computer,
  enter the key again and add jobs again.
- Your **settings** can be copied. Turn on VS Code **Settings Sync**, or paste the `revincoder.*` lines from the old computer's
  `settings.json` (`Ctrl+Shift+P`, **Preferences: Open User Settings (JSON)**) into the new one.

### Install problems

| Problem | Fix |
|---|---|
| `Extension manifest not found` | You ran the build in the wrong folder. `cd` into the folder that contains `package.json` (check with `dir` / `ls`). |
| `npx`/`node`/`npm` "is not recognized" | Node.js is not installed, or the terminal was open before you installed it. Install Node.js (Step 0) and open a **new** terminal. |
| PowerShell: "running scripts is disabled on this system" | Run `Set-ExecutionPolicy -Scope CurrentUser RemoteSigned`, then try again. Or use `npx.cmd @vscode/vsce package --allow-missing-repository --skip-license`. |
| `code` is "not recognized" | Use **Install from VSIX...** in the Extensions panel, or add the `code` command (Step 0). |
| `ENOENT ... .vsix` | The `.vsix` is not in the folder you are in. `cd` to where it is, or give the full path in quotes. |
| `npx` fails to download (network error) | Needs internet. Behind a company proxy or firewall, use Route A with a `.vsix` built on another computer. |
| Commands missing after install | Close **all** VS Code windows and reopen. Confirm with `code --list-extensions`. |

---

## Using it

| Do this | How |
|---|---|
| Add jobs | `Ctrl+Shift+P`, **Revincoder: Add jobs to queue** (or **Import jobs from a text file**: one job per line, or blank-line separated) |
| Start the queue | **Revincoder: Run queue** |
| One project right now | **Revincoder: Build one project now** (types into the open folder's root) |
| **Pause / resume** | `Ctrl+Alt+Q`. Typing freezes and continues from the exact character. |
| **Cancel and save progress** | `Ctrl+Alt+Shift+Q`. Press `Ctrl+Alt+Q` later (even after closing VS Code, same folder) to continue exactly where it stopped. |
| See / remove queued jobs | **Revincoder: View / remove queued jobs** |
| Re-run failed jobs, restart from scratch, clear | **Revincoder: Queue maintenance** |
| Change the API key | **Revincoder: Set Gemini API key** |

On macOS use `Cmd+Alt+Q` and `Cmd+Alt+Shift+Q`.

### What it does
- **Streams while Gemini writes:** code appears as it is generated, not after.
- **Human-like typing:** adjustable speed, jitter, thinking pauses and breaks.
- **Mistakes:** wrong key, swapped, skipped or doubled letters. Some are noticed straight away (backspace and slow retype),
  others are left in and fixed a few lines later when the cursor jumps back up, highlights the mistake, fixes it, and returns
  to the current line. It sometimes scrolls up to re-read earlier lines too.
- **Keeps the computer awake** while it works (not while paused or idle). It does not fake keyboard/mouse input, so apps that
  measure real input can still show you as away.
- **Survives interruptions:** laptop sleep, Wi-Fi drops and VS Code closing. It waits for the network and continues the
  generation from where it stopped (about 11 tries before giving up and keeping the job resumable).
- **Quiet:** no status bar item and no "finished" pop-ups by default. Errors, warnings and the "Run queue now" prompt still show.

### Settings
Open Settings (`Ctrl+,`) and search `revincoder`. The main ones:

| Setting | Default | Meaning |
|---|---|---|
| `charsPerMinute` | 900 | Typing speed (300 is about 60 words per minute) |
| `typoRate` | 0.04 | Chance of a mistake per letter |
| `lateFixChance` | 0.35 | Share of mistakes fixed later by jumping back up to them |
| `reviewChance` | 0.04 | Chance per new line of scrolling up to re-read earlier code |
| `breakEveryMinutes` / `breakSeconds` | 20 / 60 | Coffee-break timing (0 turns breaks off) |
| `jobPauseSeconds` | 20 | Pause between one job and the next |
| `separateFolders` | true | Put each queued job in its own numbered folder |
| `keepAwake` / `keepDisplayOn` | true / true | Prevent sleep / screen-off while working |
| `showStatusBar` / `showProgressMessages` | false / false | Show the status bar item / extra pop-ups |
| `model` | `gemini-3.8-flash` | Gemini model that writes the code |

Others: `variance`, `typoNoticeMaxChars`, `correctionSlowdown`, `thinkChance`, `thinkMinMs`, `thinkMaxMs`,
`fileSwitchMs`, `lateFixMinChars`, `lateFixMaxChars`.

---

## Troubleshooting

| Problem | Fix |
|---|---|
| No "Revincoder" commands in the palette | Run `code --list-extensions` and look for `revin.revincoder`. If it is there, **close all VS Code windows** and reopen. If not, install again. |
| `Model "..." not found` | Google retires models. Set `revincoder.model` to a current one, e.g. `gemini-3.8-flash` or `gemini-3.5-flash-lite`. |
| Rate limit / quota messages | Free keys have small limits. It retries by itself. If it keeps failing, switch to `gemini-3.5-flash-lite` or wait a while. |
| `Gemini rejected the API key` | Run **Revincoder: Set Gemini API key** and paste a fresh key. |
| "Could not find any files in the model output" | The raw reply is saved as `revincoder.raw.txt` in your folder. Try a smaller or clearer request. |
| Laptop still goes to sleep | Windows needs PowerShell allowed to run; Linux needs `systemd-inhibit`. Otherwise set Windows power options to never sleep while plugged in. |
| A job stopped half way | Press `Ctrl+Alt+Q` (or **Run queue**). It continues from the exact spot. |
| Want a job to start over | **Revincoder: Queue maintenance**, **Restart interrupted jobs from scratch**. |
| `Ctrl+Alt+Q` does nothing | Another app may use that shortcut. Change it in `Ctrl+K Ctrl+S` by searching "Revincoder". |

Do not edit or move a half-typed file while a job is stopped, or it may not continue cleanly.

---

## Run a plan file (no Gemini needed)

**Revincoder: Run a plan (JSON)** types files from a plan you wrote yourself (pausable, but not resumable after cancel):
```json
{ "files": [ { "path": "src/app.js", "content": "console.log('hi');\n" } ] }
```
`example.plan.json` is included as a sample.
