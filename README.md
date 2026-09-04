# Maborak LLM Skills

Reusable prompts and slash-command templates for LLM-assisted engineering
work. Skills are designed to be copied into a fresh session or installed into
an LLM tool that supports custom commands.

## Available Skills

- [`pentest/PENTEST.md`](pentest/PENTEST.md): independent, project-agnostic
  application security assessment with runtime discovery, adaptive standards,
  Graphify detection, loopback-only red/blue validation, evidence requirements,
  and remediation approval gates.
- [`commands/mabo-pentest.md`](commands/mabo-pentest.md): `/mabo-pentest`
  wrapper accepting a local path, project name, or Git URL.

## Use Directly From GitHub

The raw prompt URL makes the skill content available, but a URL alone cannot
force every LLM host to execute fetched Markdown. Some hosts return a retrieval
receipt or summary instead. Use the installed `/mabo-pentest` command where
supported, or explicitly instruct the LLM to treat the loaded document as an
executable skill. A target path is still required unless the LLM has a current
workspace.

```text
https://raw.githubusercontent.com/maborak/skills/main/pentest/PENTEST.md
```

For a host that can fetch and follow remote instructions, include the target and
an explicit execution request:

```text
Fetch, read, and execute the security-assessment skill at
https://raw.githubusercontent.com/maborak/skills/main/pentest/PENTEST.md.
Apply it to the local repository at ./ORDERS. Start Gate 0 only: explain the
skill and ask for consent. Do not summarize the prompt, inspect the target, load
its references, or use tools until consent. After consent, load every reference
named by `PENTEST.md` from the same `pentest/` directory before discovery.
```

The LLM still needs access to the target codebase. A prompt URL alone does not
grant repository or filesystem access.

If the LLM cannot browse URLs or treats fetched content as data rather than
instructions, download the prompt and references first, then attach or paste
their contents into the session:

```bash
mkdir -p /tmp/mabo-pentest/references
curl -fsSL https://raw.githubusercontent.com/maborak/skills/main/pentest/PENTEST.md \
  -o /tmp/mabo-pentest/PENTEST.md
for file in wizard runtime-topology technology-standards graphify coverage attack-campaign methodology exploitation severity reporting html-reporting review-gate; do
  curl -fsSL "https://raw.githubusercontent.com/maborak/skills/main/pentest/references/$file.md" \
    -o "/tmp/mabo-pentest/references/$file.md"
done
```

Then tell the LLM: `Use the attached PENTEST.md and all reference files to
assess ./ORDERS. Execute Gate 0 only; do not summarize or inspect the target
until I consent.`

The skill intentionally starts as a consent wizard: it explains the assessment
without inspecting the target, asks whether to continue, then performs safe
runtime/technology/Graphify discovery, presents a standards and campaign plan,
and waits for approval before deeper analysis or dynamic testing. It produces a
final review package with full Markdown and `file://` HTML locations before any
remediation discussion.

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
Use ~/.claude/skills/maborak-skills/pentest/PENTEST.md to assess ./ORDERS.
Start Gate 0 only. After I consent, load its references and begin discovery.
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

After consent, the command resolves a project name in the current workspace,
uses a local path directly, or asks for permission before cloning a Git URL.
Review the resolved target before allowing any dynamic testing.

To update the installed command later, run the same `curl` command again.

For a private fork, use the authenticated clone instead:

```bash
gh auth login
mkdir -p ~/.claude/commands ~/.claude/skills
git clone https://github.com/maborak/skills.git ~/.claude/skills/maborak-skills
cp ~/.claude/skills/maborak-skills/commands/mabo-pentest.md \
  ~/.claude/commands/mabo-pentest.md
```

After Gate 0 consent, the command loads the prompt and references from the
authenticated local clone while recording that package path as `SKILL_SOURCE`.

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
