# Maborak LLM Skills

Reusable prompts and slash-command templates for LLM-assisted engineering
work. Skills are designed to be copied into a fresh session or installed into
an LLM tool that supports custom commands.

## Available Skills

- [`pentest/PENTEST.md`](pentest/PENTEST.md): independent, project-agnostic
  application security assessment with automatic target/tooling bootstrap,
  strict project-only containment, adaptive standards, Graphify detection,
  loopback-only red/blue validation, an append-only LLM execution trace, a
  complete chronological test report, and remediation approval gates.
- [`commands/mabo-pentest.md`](commands/mabo-pentest.md): `/mabo-pentest`
  wrapper accepting a local path, project name, or Git URL.

## Use Directly From GitHub

The raw prompt URL makes the skill content available, but a URL alone cannot
force every LLM host to execute fetched Markdown. Some hosts return a retrieval
receipt or summary instead. Use the installed `/mabo-pentest` command where
supported, or explicitly instruct the LLM to treat the loaded document as an
executable skill. A target path is still required unless the LLM has a current
project directory.

```text
https://raw.githubusercontent.com/maborak/skills/main/pentest/PENTEST.md
```

For a host that can fetch and follow remote instructions, include the target and
an explicit execution request:

```text
Fetch, read, and execute the security-assessment skill at
https://raw.githubusercontent.com/maborak/skills/main/pentest/PENTEST.md.
Apply it to the local repository at ./ORDERS. Run its bounded bootstrap now:
load the bootstrap references named by the prompt from the same `pentest/`
directory, inspect the local target read-only, detect its architecture and
available assessment tools, and present the target-specific plan for approval.
Defer later-gate references and do not only summarize the prompt.
Do not start services, install tools, run scanners, send application requests,
or alter the target before I approve the presented plan. Treat ./ORDERS as the
only project root: do not inspect parents, siblings, home/global configuration,
other repositories, or paths reached through escaping symlinks. Write every
artifact under ./ORDERS/.mabo-pentest/. Maintain the required append-only
llm-trace.jsonl so another LLM can audit every observable action and result.
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
for file in wizard project-boundary llm-trace tool-bootstrap runtime-topology technology-standards graphify coverage attack-campaign methodology exploitation severity reporting html-reporting review-gate; do
  curl -fsSL "https://raw.githubusercontent.com/maborak/skills/main/pentest/references/$file.md" \
    -o "/tmp/mabo-pentest/references/$file.md"
done
```

Then tell the LLM: `Use the attached PENTEST.md and all reference files to
bootstrap ./ORDERS now. Inspect it read-only, detect the exact toolchain and
assessment plan, present the project-specific wizard summary, and wait for my
approval before executing the assessment. Treat ./ORDERS as the only project
root and never search outside it. Do not create or advertise a final report
until the approved assessment reaches and passes its final review gate. Maintain
the internal llm-trace.jsonl and provide its path and SHA-256 digest.`

The skill intentionally starts as a bootstrap wizard. Invocation performs
bounded read-only target, runtime, technology, Graphify, and tool-readiness
discovery, then presents what the tool does and the exact target-specific work
it proposes. Approval starts the listed analysis and local dynamic testing. A
validated report is generated only after that approved work has a final
disposition, and a separate decision is still required before remediation. All
target input and audit output remain under the selected project root; external
references are reported but never followed. The final Markdown and HTML reports
include the complete assessed-input inventory, command/activity timeline,
coverage denominators, and every test with expected behavior, actual behavior,
result, and evidence. The append-only JSONL trace records every observable
operation for later independent LLM review without secret values or hidden
chain-of-thought.

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
Bootstrap the target and tools now, present the exact plan, and wait for my
approval before running the assessment. Use ./ORDERS as the sole project root
and do not inspect target data anywhere else. The exact named skill files and
exact selected tool executables are the only external resources; neither is
assessment input.
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

The command treats a project name only as an exact relative path or uses a
supplied local path immediately. It never searches parent directories, siblings,
home directories, or other repositories. It asks for permission and an exact
destination before cloning a Git URL. Review the detected target, toolchain, and
proposed work before approving execution.

To update the installed command later, run the same `curl` command again.

For a private fork, use the exact local prompt path shown above or configure the
host to supply that clone's `pentest/` directory as `SKILL_SOURCE`. The command
never searches the filesystem for a private package; without an explicit source
it uses the canonical public package.

## Manual Use

Open [`pentest/PENTEST.md`](pentest/PENTEST.md), copy the prompt into a fresh
LLM session, and provide the local target path. It will bootstrap the target and
present the selected tools and assessment plan before proceeding. Review final
findings and proposed changes before approving any remediation. The prompt
forbids discovery outside the selected project and always forbids commits.
Application patches require explicit remediation approval.

## Adding A Skill

Add a self-contained Markdown file describing its purpose, inputs, safety
constraints, expected outputs, and verification steps. Put slash-command
templates in `commands/`, update this catalog, and keep generated audit output
out of version control.
