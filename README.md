# osiris-hardware: Team Workflow Guide

This repo holds all Osiris KiCad projects. Read this once before touching anything. It covers: what git actually is, one-time setup, the daily workflow, and the rules that keep us from destroying each other's work.

---

## 1. What git is (2 minutes, skip if you know)

Git tracks every version of every file, forever. GitHub is the website where our shared copy lives.

Four words you'll see constantly:

- **Clone**: download the repo to your computer. You do this once.
- **Branch**: a personal workspace. Think of `main` as the master document everyone trusts, and a branch as your own scratch copy. You make changes on your branch; `main` stays untouched until your work is approved.
- **Commit**: a saved checkpoint of your changes, with a message describing what you did. Like hitting save, but with a note attached.
- **Merge (via Pull Request)**: asking for your branch's changes to be folded into `main`. On GitHub this is called a Pull Request (PR). Someone reviews it, approves it, and clicks merge. That's the only way changes reach `main`.

You never edit `main` directly. The repo is configured to block it.

## 2. One-time setup

### Install
1. Install git: https://git-scm.com/downloads (Mac users: it may already be installed, check with `git --version` in Terminal)
2. Install KiCad 10: https://www.kicad.org/download/
3. Make a GitHub account and get added to the rocketproplab org (ask Edgar).

### Clone the repo
Open a terminal and run:

```bash
git clone https://github.com/rocketproplab/osiris-hardware.git
cd osiris-hardware
```

### Point KiCad at our shared library (do this BEFORE opening any project)
Order matters here. KiCad only auto-substitutes the path variable into libraries added after the variable exists.

1. Open KiCad. Go to **Preferences → Configure Paths**.
2. Click **+** and add:
   - Name: `RPL_LIB`
   - Path: the full path to the `lib` folder inside your cloned repo (e.g. `/Users/you/osiris-hardware/lib`)
3. Click OK.

That's it. Board projects in this repo reference the team library through `${RPL_LIB}`, so they'll open correctly on your machine now.

### Tell git who you are (first time only)
```bash
git config --global user.name "Your Name"
git config --global user.email "you@ucsd.edu"
```

## 3. The daily workflow (the only 6 commands you need)

Every piece of work follows the same loop. No exceptions.

```bash
# 1. Get the latest version of main
git checkout main
git pull

# 2. Make your personal workspace (name it after the work)
git checkout -b feature/lsm6dsox-footprint

# 3. Do your work in KiCad. Save your files.

# 4. Checkpoint your work
git add -A
git commit -m "Add LSM6DSOX footprint and 3D model"

# 5. Upload your branch to GitHub
git push -u origin feature/lsm6dsox-footprint

# 6. Open a Pull Request
# Go to the repo on github.com. A yellow banner will offer
# "Compare & pull request". Click it, describe what you did, submit.
```

After someone approves and merges your PR, your work is in `main`. Delete the branch (GitHub offers a button) and start the loop again for your next task.

**If you're ever confused about what state you're in:** run `git status`. It tells you what branch you're on and what's changed. When in doubt, ask before running commands you don't understand. Nothing in git is truly lost, but untangling takes time.

## 4. Repo layout

```
osiris-hardware/
  lib/                    THE SHARED TEAM LIBRARY
    symbols/              rpl.kicad_sym (schematic symbols)
    footprints/rpl.pretty/  (PCB footprints)
    3dmodels/rpl.3dshapes/  (3D models)
  boards/                 ONE FOLDER PER BOARD
    demo-board/
  fab/                    empty on purpose, see Releases below
```

## 5. The two kinds of work, and the rules for each

### Library work: symbols, footprints, 3D models. OPEN TO EVERYONE.
This is where most people contribute, especially at first. It's safe parallel work because you're adding new parts, not editing shared board files.

Rules:
- Every symbol must have two fields filled in: **MPN** (manufacturer part number) and **Datasheet** (a link). No exceptions. A part without a datasheet link doesn't get merged.
- Before making a part, check it doesn't already exist in the library.
- One part (or one related set of parts) per branch/PR. Small PRs get reviewed fast.

### Board work: schematics and PCB layout. ONE OWNER AT A TIME.
KiCad board files cannot be merged. If two people edit the same `.kicad_pcb`, one person's work gets thrown away. So:

- Each folder in `boards/` has exactly one owner at a time.
- If you don't own the board, don't touch its folder. Feed the owner footprints through library PRs instead.
- Ownership is announced in the team channel and handed off explicitly.

## 6. Things that will bite you if ignored

- **Always pull before branching.** Skipping step 1 means you're building on stale files, and your PR will conflict.
- **Never commit to main.** It's blocked, but don't try.
- **Don't commit generated junk.** The `.gitignore` handles backups, autosaves, and `.kicad_prl` (per-user view settings). If git shows changes to files you didn't knowingly edit, ask before committing.
- **Fab outputs (gerbers) never go in the repo.** When a board is sent to fab, the owner generates gerbers, zips them, and attaches the zip to a GitHub Release with a tag like `demo-board-revA`. That tag is our permanent record of exactly what was manufactured.

## 7. Cheat sheet

| I want to... | Command |
|---|---|
| See what state I'm in | `git status` |
| Get latest main | `git checkout main` then `git pull` |
| Start new work | `git checkout -b feature/short-name` |
| Save a checkpoint | `git add -A` then `git commit -m "message"` |
| Upload my branch | `git push -u origin feature/short-name` |
| Get my work into main | Open a Pull Request on github.com |

Questions: ask in the AV channel. Asking early beats untangling later.
