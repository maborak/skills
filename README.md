# Maborak LLM Skills

Reusable prompts and slash-command templates for LLM-assisted engineering
work. Skills are designed to be copied into a fresh session or installed into
an LLM tool that supports custom commands.

## Available Skills

- [`pentest/PENTEST.md`](pentest/PENTEST.md): independent, project-agnostic security assessment
  with loopback-only testing, evidence requirements, and approval gates.
- [`commands/mabo-pentest.md`](commands/mabo-pentest.md): `/mabo-pentest`
  wrapper accepting a local path, project name, or Git URL.

## Use Directly From GitHub

For a public repository, copy the raw prompt URL into an LLM session:

```text
https://raw.githubusercontent.com/maborak/skills/main/pentest/PENTEST.md
```

Then ask the LLM to read that URL and assess a local target, for example:

```text
Read https://raw.githubusercontent.com/maborak/skills/main/pentest/PENTEST.md
and use it to assess the local repository at ./ORDERS.
```

The LLM still needs access to the target codebase. A prompt URL alone does not
grant repository or filesystem access.

When using the prompt remotely, tell the LLM to load the references from the
same `pentest/` GitHub directory. The prompt records that source location in
the report so the skill files and target files are not confused.

This repository is currently private, so an LLM without GitHub credentials
cannot fetch the raw URLs. For a private repository, authenticate GitHub and
clone the skill package locally instead:

```bash
gh auth login
mkdir -p ~/.claude/skills
git clone https://github.com/maborak/skills.git ~/.claude/skills/maborak-skills
```

Then provide the local prompt path to the LLM:

```text
Read ~/.claude/skills/maborak-skills/pentest/PENTEST.md and its references,
then assess ./ORDERS.
```

## Install `/mabo-pentest` In Claude Code

Install the command globally from a public repository:

```bash
mkdir -p ~/.claude/commands
curl -fsSL https://raw.githubusercontent.com/maborak/skills/main/commands/mabo-pentest.md \
  -o ~/.claude/commands/mabo-pentest.md
```

Restart Claude Code if it was already running, then use:

```text
/mabo-pentest ORDERS
/mabo-pentest ./path/to/repository
/mabo-pentest https://github.com/example/project.git
/mabo-pentest
```

The command resolves a project name in the current workspace, uses a local
path directly, or asks for permission before cloning a Git URL. Review the
resolved target before allowing any dynamic testing.

To update the installed command later, run the same `curl` command again.

For this private repository, use the authenticated clone instead:

```bash
gh auth login
mkdir -p ~/.claude/commands ~/.claude/skills
git clone https://github.com/maborak/skills.git ~/.claude/skills/maborak-skills
cp ~/.claude/skills/maborak-skills/commands/mabo-pentest.md \
  ~/.claude/commands/mabo-pentest.md
```

The command then loads the prompt and references from the authenticated local
clone, while recording that local package path as `SKILL_SOURCE`.

## Manual Use

Open [`pentest/PENTEST.md`](pentest/PENTEST.md), copy the prompt into a fresh
LLM session, and provide the local target path. Review findings and proposed
changes before approving any remediation. The prompt forbids commits and
application patches unless you explicitly approve them.

## Adding A Skill

Add a self-contained Markdown file describing its purpose, inputs, safety
constraints, expected outputs, and verification steps. Put slash-command
templates in `commands/`, update this catalog, and keep generated audit output
out of version control.
