# 🚀 GitHub Action Pilot

![License](https://img.shields.io/github/license/your-org/github-action-pilot)

> **Templated CI/CD Workflow Syncing Across GitHub Repositories — Helm-style.**

---

## ✨ Overview

**GitHub Action Pilot** is a developer-first CLI and GitHub Action that lets you **sync centralized, templated workflows** across multiple GitHub repositories using a **Helm-like architecture**.

It's built to scale GitHub Actions management in organizations, keeping your pipelines DRY, versioned, and consistent.

---

## 🚀 Key Features

- **Multi‑Repository Sync**: Apply one or more workflow templates to any number of repositories with a single command.
- **Helm‑Style Templating**: Supports `.tpl` macros, includes, and dynamic variable injection.
- **Per‑Repository Overrides**: Customize branches, commit messages, paths, variables, and template selection per repository.
- **Regex‑Driven Template Selection**: Use patterns to precisely control which templates apply to which repositories.
- **Interactive Change Preview**: Before applying changes, see a side-by-side comparison of created, updated, and deleted files with toggles and highlights.
- **Non-interactive mode** for automation & CI/CD
- **State Management**: Tracks previously synced workflows and automatically cleans up obsolete files.
- **Extensible Provider Model**: Built-in support for GitHub, with support for other platforms planned.

---

## 📦 Installation

Install the tool using pip:

```bash
pip install https://github.com/r3d-shadow/git-pilot/releases/download/v0.1.3/git_pilot-0.1.3-py3-none-any.whl
```

---

## Usage

## 🚀 Usage

### 🧱 1. Bootstrap Template Directory (Only if needed)

If you haven't already created templates, you can scaffold a starter structure locally:

```bash
git-pilot init --template-dir templates
```

This sets up a starter folder structure where you can customize the workflow .j2 templates and the values.yml file to match your specific CI/CD requirements.

### 🤖 2. Enable Auto-Sync via GitHub Actions

To automatically render and sync CI workflows on every template or value change, create this workflow in your repo:

```yaml
# .github/workflows/git-pilot-sync.yml
name: Git Pilot Sync

on:
  push:
    paths:
      - .github/git-pilot/values.yaml
      - .github/git-pilot/templates/**

jobs:
  sync:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Run Git Pilot
        uses: r3d-shadow/github-action-pilot@v0.0.1
        with:
          token: ${{ secrets.GITHUB_TOKEN }}
          template_dir: .github/git-pilot/templates
          state_file: .git-pilot-state.json
          values: .github/git-pilot/values.yaml

      - name: Commit updated state file
        run: |
          git config user.name "github-actions[bot]"
          git config user.email "github-actions[bot]@users.noreply.github.com"
          git add .git-pilot-state.json
          git commit -m "chore: update Git Pilot state file" || echo "No changes to commit"
          git push
```

> ✅ This workflow ensures your repo’s CI workflows stay consistent with centrally maintained templates — while tracking sync history via `.git-pilot-state.json`.

### Options:

| Flag          | Description                            |
| ------------- | -------------------------------------- |
| `token`     | GitHub Personal Access Token           |
| `template_dir` | Path to the root template directory    |
| `values`    | Path to the config file (`values.yml`) |

---

## 🧩 Configuration (`values.yml`)

The configuration file defines global defaults and per-repo overrides for:

* Branch name
* Commit message
* Target directory for rendered templates
* Regex patterns for template selection
* Variables to inject into templates

See the [Configuration Reference](https://github.com/r3d-shadow/git-pilot/blob/main/docs/configuration.md) for a detailed breakdown.

---

## 🧠 Templating

`git-pilot` supports Helm-style rendering using Jinja2, including:

* Custom delimiters to avoid conflicts with GitHub Actions syntax
* Shared macro files and reusable partial templates
* Built-in helper functions like `indent`, `to_yaml`, `tpl()`, etc.

See the [Templating Guide](https://github.com/r3d-shadow/git-pilot/blob/main/docs/templating.md) for structure and usage examples.

---

## 💾 State Management

A local JSON state file tracks:

* Which templates were applied to which repositories and branches
* SHA of the rendered content to detect changes
* Obsolete workflows to remove when templates are no longer matched

This ensures safe, idempotent operations and automatic cleanup.

---

## 🧪 Interactive Comparison Viewer

Before applying changes, `git-pilot` presents a full summary:

* **Created**: New files to be added
* **Updated**: Modified files with side-by-side diffs
* **Deleted**: Stale or obsolete files to be removed

You can visually inspect changes before confirming. Nothing is pushed until confirmed.

---