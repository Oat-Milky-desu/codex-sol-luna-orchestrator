# Codex Sol Orchestrator + Luna Subagents

A configurable Codex setup where GPT-6 Sol is the root/orchestrator and reviewer, while GPT-6 Luna is the default and pinned model for execution subagents.

All four profiles use the same model topology: GPT-6 Sol at `medium` reasoning for the root/orchestrator and reviewer, and GPT-6 Luna at `max` reasoning for execution roles and default subagents. The Pro and Plus profile names remain as installer choices; the `max-2-subagents` profiles differ only in their concurrency limit.

## Layout

```text
.
├── profiles/
│   ├── pro/
│   │   ├── codex/           (config.toml and agents/*.toml)
│   │   └── agents/          (skills/sol-orchestrator/SKILL.md)
│   ├── plus/
│   │   ├── codex/           (config.toml and agents/*.toml)
│   │   └── agents/          (skills/sol-orchestrator/SKILL.md)
│   ├── pro-max-2-subagents/  (Pro with a concurrent subagent limit of 2)
│   │   ├── codex/           (config.toml and agents/*.toml)
│   │   └── agents/          (skills/sol-orchestrator/SKILL.md)
│   └── plus-max-2-subagents/ (Plus with a concurrent subagent limit of 2)
│       ├── codex/           (config.toml and agents/*.toml)
│       └── agents/          (skills/sol-orchestrator/SKILL.md)
├── guides/
│   ├── fast-iteration.md
│   ├── complex-repo-work.md
│   ├── routine-coding.md
│   ├── full-orchestration.md
│   ├── plus-plan.md
│   └── token-usage.md
├── scripts/
│   └── token_usage.py
├── AGENTS.md
├── setup.sh
├── setup.ps1
└── LICENSE
```

## Current profiles

| Role or setting | `pro` | `plus` | `pro-max-2-subagents` | `plus-max-2-subagents` |
|---|---|---|---|---|
| Orchestrator | GPT-6 Sol — medium | GPT-6 Sol — medium | GPT-6 Sol — medium | GPT-6 Sol — medium |
| Explorer, worker, tester, researcher | GPT-6 Luna — max | GPT-6 Luna — max | GPT-6 Luna — max | GPT-6 Luna — max |
| Default subagent | GPT-6 Luna — max | GPT-6 Luna — max | GPT-6 Luna — max | GPT-6 Luna — max |
| Independent reviewer | GPT-6 Sol — medium | GPT-6 Sol — medium | GPT-6 Sol — medium | GPT-6 Sol — medium |
| Concurrent subagent limit | 4 | 4 | 2 | 2 |

### Pro — `profiles/pro/codex/config.toml`

```toml
model = "gpt-6-sol"
model_reasoning_effort = "medium"

approval_policy = "on-request"
sandbox_mode = "workspace-write"

[agents]
enabled = true
max_concurrent_threads_per_session = 4
default_subagent_model = "gpt-6-luna"
default_subagent_reasoning_effort = "max"
```

The `plus` and `pro` configuration files use the same model and effort settings. The `max-2-subagents` variants keep those settings and set the concurrent subagent limit to 2.

The installer copies `profiles/<profile>/codex` to `.codex` and
`profiles/<profile>/agents` to `.agents` in the target repository. Each profile
is ready to copy, with no configuration rewriting during setup.

Each role file is explicitly pinned to its intended model: Luna for explorer, worker, tester, and researcher; Sol for reviewer. This means changing only `default_subagent_model` will affect generic spawned agents, but not the named roles.

The four Luna role files explicitly set `model_reasoning_effort = "max"` in all profiles. The reviewer explicitly uses Sol at `medium` effort in all profiles.

When updating an existing installation, copy the role files along with `config.toml` from the selected profile. Replace `<profile>` below with `pro`, `plus`, `pro-max-2-subagents`, or `plus-max-2-subagents`.

Keep the explicit `model` pins in the role files to preserve this topology. Removing the reviewer's model pin makes it inherit the Luna default; remove only reasoning overrides if you want roles to use the configured default effort.

## Project setup

Clone this repository:

```bash
git clone https://github.com/Oat-Milky-desu/codex-sol-luna-orchestrator.git
cd codex-sol-luna-orchestrator
```

The target project must already exist and must be different from this setup
repository.

### macOS and Linux

Run the shell installer:

```bash
./setup.sh
```

### Windows

Run the PowerShell installer from Windows PowerShell:

```powershell
powershell -ExecutionPolicy Bypass -File .\setup.ps1
```

With PowerShell 7, you can use:

```powershell
pwsh -File .\setup.ps1
```

### Installer prompts

When asked for the target repository, enter its absolute or relative path. For
example:

```text
Target repository path: ../my-project
```

Next, choose your Codex plan:

```text
Choose Profile to install
  1) Pro  - GPT-6 Sol (medium) orchestrates, GPT-6 Luna (max) executes, GPT-6 Sol (medium) reviews
  2) Plus - GPT-6 Sol (medium) orchestrates, GPT-6 Luna (max) executes, GPT-6 Sol (medium) reviews
  3) Pro (max 2 subagents) - GPT-6 Sol (medium) orchestrates, GPT-6 Luna (max) executes, GPT-6 Sol (medium) reviews
  4) Plus (max 2 subagents) - GPT-6 Sol (medium) orchestrates, GPT-6 Luna (max) executes, GPT-6 Sol (medium) reviews
Select plan [1-4] (default 1):
```

All profiles set the root and reviewer to Sol at medium effort. Explorer,
worker, tester, researcher, and default subagents use Luna at max effort. The
max-2-subagents profiles change only the concurrency limit from 4 to 2.

The installer then asks whether to install each component:

- `profiles/<profile>/codex` contains the root configuration and agent role profiles, installed as `.codex`.
- `profiles/<profile>/agents` contains the `sol-orchestrator` skill, installed as `.agents`.
- `AGENTS.md` gives Codex the project-level orchestration instructions. If it
  already exists, setup appends the instructions and preserves its contents.
  Re-running setup skips the append when the same instructions are already
  present. Symbolic links and incompatible targets are skipped.

Press Enter or answer `y` to install a component; answer `n` to skip it. All
three components are selected by default.

If a component already exists, the installer lists the exact paths that would
be overwritten and asks again before making changes:

```text
WARNING: the following existing files will be overwritten:
  - .codex/config.toml
Update .codex? New files will be added; only paths listed above will be replaced. [y/N]
```

Existing-file updates default to `n`. If approved, missing files are added and
only the listed paths are replaced. Other files already present in the target
component remain untouched.

After setup, launch Codex from the target repository. Project-scoped `.codex`
configuration is loaded only for trusted projects.

See `guides/` for copy-paste model presets and the Sol + Luna topology. The
guides are intentionally separate from the installers so you can review and
adapt settings for your Codex version without changing a global config
automatically.

## Personal/global setup

For agents, copy the TOML files from `profiles/<profile>/codex/agents/` to:

```text
~/.codex/agents/
```

For the skill, copy `profiles/<profile>/agents/skills/sol-orchestrator/` to:

```text
~/.agents/skills/sol-orchestrator/
```

Merge the settings from `profiles/pro/codex/config.toml` (Pro) or `profiles/plus/codex/config.toml`
(Plus) into your existing:

```text
~/.codex/config.toml
```

Do not blindly overwrite your existing global config if you already have MCP servers, providers, permissions, or other settings.

## Using the skill

Codex may select the skill automatically when the task matches its description.

You can also invoke it explicitly from Codex CLI or the IDE extension with:

```text
$sol-orchestrator
```

Example prompt:

```text
$sol-orchestrator

Implement the new invoice export endpoint.
Have explorer map the existing invoice/export path first.
Use workers for bounded implementation, tester for verification,
and reviewer for an independent final review.
```

## Suggested topology

```text
                 GPT-6 Sol
             root / orchestrator
                      |
      +---------------+---------------+
      |               |               |
   explorer          worker         researcher
     Luna             Luna             Luna
      |               |
      +-------+-------+
              |
           tester
            Luna
              |
          reviewer
           Sol
              |
              v
         GPT-6 Sol
      integrate + verify
```

## Tuning

For cheaper/faster runs:
- keep the shared Sol `medium` and Luna `max` reasoning settings
- use the `fast` service tier where supported
- select a max-2 profile to limit concurrent subagents to 2

For larger codebases:
- keep the shared Sol `medium` and Luna `max` reasoning settings
- use the standard service tier where supported
- use the standard profiles' limit of 4 concurrent subagents when tasks are independent

For strict parent/child separation:
- keep explorer/reviewer/researcher read-only
- keep worker/tester workspace-write
- leave the root in workspace-write so it can integrate changes

## Token usage

Orchestration is not free: the root stays in the loop for the whole task and
every subagent carries its own context. Usage depends on repository size and
task shape, so there is no single number. `scripts/token_usage.py` reads the
rollout logs Codex already writes under `~/.codex/sessions` and reports usage
per thread, role, and model, plus the change in your 5-hour and 7-day rate
limit windows:

```bash
scripts/token_usage.py --list --date 2026-09-07
scripts/token_usage.py --latest --date 2026-09-07
```

See [`guides/token-usage.md`](guides/token-usage.md) for a measurement
protocol, one sample run with real numbers, and tips for reducing usage.

For the shared model and reasoning settings in a manual or global setup, see
[`guides/full-orchestration.md`](guides/full-orchestration.md):

```toml
# Root
model = "gpt-6-sol"
model_reasoning_effort = "medium"
```

## Important behavior

Explicit model choices during a spawn override `[agents]` defaults. Custom agent files that specify `model` or `model_reasoning_effort` also take precedence over inherited defaults.

The execution role files are pinned to Luna intentionally, while the reviewer is pinned to Sol for independent final review. Each role keeps the same effort across all four profiles.

## License

Licensed under the [Apache License 2.0](LICENSE).
