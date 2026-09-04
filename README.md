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

For a public repository, give the LLM both the raw prompt URL and an explicit
task. Pasting only a URL does not execute its contents; the model may only
summarize or acknowledge the document.

```text
https://raw.githubusercontent.com/maborak/skills/main/pentest/PENTEST.md
```

Use a message like this:

```text
Read and follow the security-assessment instructions at
https://raw.githubusercontent.com/maborak/skills/main/pentest/PENTEST.md.
Also load all reference files in its `references/` directory. Apply them now
to the local repository at ./ORDERS. Start the discovery-first wizard: explain
the skill, resolve the target, record the baseline, perform safe discovery, and
present a plan for approval. Do not only summarize the prompt.
```

The LLM still needs access to the target codebase. A prompt URL alone does not
grant repository or filesystem access.

If the LLM cannot browse URLs, download the prompt and references first, then
paste their contents into the session:

```bash
mkdir -p /tmp/mabo-pentest/references
curl -fsSL https://raw.githubusercontent.com/maborak/skills/main/pentest/PENTEST.md \
  -o /tmp/mabo-pentest/PENTEST.md
for file in methodology coverage exploitation reporting severity html-reporting; do
  curl -fsSL "https://raw.githubusercontent.com/maborak/skills/main/pentest/references/$file.md" \
    -o "/tmp/mabo-pentest/references/$file.md"
done
```

Then tell the LLM: `Use the attached PENTEST.md and reference files to assess
./ORDERS now. Do not summarize the instructions; execute them.`

The skill intentionally starts as a wizard: it explains the assessment,
performs safe discovery, presents a plan, and waits for approval before deeper
analysis or dynamic testing.

When using the prompt remotely, tell the LLM to load the references from the
same `pentest/` GitHub directory. The prompt records that source location in
the report so the skill files and target files are not confused.

If you use the skill from a private fork, authenticate GitHub and clone the
skill package locally instead:

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

For a private fork, use the authenticated clone instead:

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
