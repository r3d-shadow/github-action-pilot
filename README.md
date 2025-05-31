# 🚀 GitHub Action Pilot

![License](https://img.shields.io/github/license/r3d-shadow/github-action-pilot)

> **Templated CI/CD Workflow Syncing Across GitHub Repositories — Helm-style.**

---

## 🔗 Project Repository

This project is part of the broader [`git-pilot`](https://github.com/r3d-shadow/git-pilot) ecosystem — a powerful CLI tool for templated, multi-repo CI/CD sync.

**GitHub Repository:**
[https://github.com/r3d-shadow/github-action-pilot](https://github.com/r3d-shadow/github-action-pilot)
**Main `git-pilot` CLI Repo:**
[https://github.com/r3d-shadow/git-pilot](https://github.com/r3d-shadow/git-pilot)

---

## ✨ Overview

**GitHub Action Pilot** is a developer-first CLI and GitHub Action that lets you **sync centralized, templated workflows** across multiple GitHub repositories using a **Helm-like architecture**.

It's built to scale GitHub Actions management in organizations, keeping your pipelines DRY, versioned, and consistent. It leverages the powerful [`git-pilot`](https://github.com/r3d-shadow/git-pilot) engine under the hood.

---

## 🚀 Key Features

* **Multi‑Repository Sync**: Apply one or more workflow templates to any number of repositories with a single command.
* **Helm‑Style Templating**: Supports `.tpl` macros, includes, and dynamic variable injection.
* **Per‑Repository Overrides**: Customize branches, commit messages, paths, variables, and template selection per repository.
* **Regex‑Driven Template Selection**: Use patterns to precisely control which templates apply to which repositories.
* **Interactive Change Preview**: Before applying changes, see a side-by-side comparison of created, updated, and deleted files with toggles and highlights.
* **Non-interactive mode** for automation & CI/CD.
* **State Management**: Tracks previously synced workflows and automatically cleans up obsolete files.
* **Extensible Provider Model**: Built-in support for GitHub, with support for other platforms planned.

---

## 📦 Installation

Install the CLI tool using pip (recommended if you want to run sync locally):

```bash
pip install git-pilot
```

The GitHub Action itself requires no install—just reference it in your workflow as shown below.

---

## Usage

### 🧱 1. Bootstrap Template Directory (Optional)

If you haven't created templates yet, bootstrap a starter structure:

```bash
git-pilot init --template-dir templates
```

### 🤖 2. Enable Auto-Sync via GitHub Actions

Add this workflow in your repo to automatically sync workflows on template or config changes:

```yaml
name: Git Pilot Sync

on:
  push:
    branches:
      - main
    paths:
      - templates/**

permissions:
  contents: write

jobs:
  sync:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Github Action Pilot
        uses: r3d-shadow/github-action-pilot@v0.2.1
        with:
          token: ${{ secrets.GIT_TOKEN }}
          template_dir: templates
          state_file: templates/.git-pilot-state.json
          values: templates/values.yml

      - name: Commit updated state file
        run: |
          git config user.name "github-actions[bot]"
          git config user.email "github-actions[bot]@users.noreply.github.com"
          git add templates/.git-pilot-state.json
          git commit -m "chore: update Git Pilot state file"
          git push
```

> ✅ This workflow uses the `git-pilot` sync engine to keep your repo’s workflows consistent and versioned.

![Demo GIF](https://github.com/r3d-shadow/git-pilot/blob/main/docs/assets/demo.gif)

---

## 🧩 Configuration (`values.yml`)

Define global defaults and per-repo overrides for branches, commit messages, target directories, regex patterns for template selection, and variables.

Detailed config docs are available here:
[https://github.com/r3d-shadow/git-pilot/blob/main/docs/configuration.md](https://github.com/r3d-shadow/git-pilot/blob/main/docs/configuration.md)

---

## 🧠 Templating

Uses the same Helm-style Jinja2 rendering engine as [`git-pilot`](https://github.com/r3d-shadow/git-pilot), supporting:

* Custom delimiters to avoid syntax conflicts
* Shared macro files and reusable partials
* Helper functions like `indent`, `to_yaml`, `tpl()`

See the templating guide:
[https://github.com/r3d-shadow/git-pilot/blob/main/docs/templating.md](https://github.com/r3d-shadow/git-pilot/blob/main/docs/templating.md)

---

## 💾 State Management

Tracks applied templates, branches, and rendered SHA in a JSON file to ensure safe syncs and cleanup.

---

## 🧪 Interactive Comparison Viewer

Before applying changes, review all created, updated, and deleted files with inline diffs and toggles. Nothing is pushed until you confirm.

---