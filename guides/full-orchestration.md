# Pro Profile: Sol + Luna Orchestration

Choose this preset when you want Sol to plan, orchestrate, and review while
Luna handles the execution roles. Select Pro in `setup.sh` or `setup.ps1`.
Setup copies `profiles/pro/codex/` to `.codex/` and
`profiles/pro/agents/` to `.agents/` in the target repository without
rewriting configuration. For manual installation, copy those same folders
and the repository's `AGENTS.md` to the target.

The topology is:

```text
Sol root (medium)
├── Luna explorer (max)
├── Luna worker (max)
├── Luna tester (max)
├── Luna researcher (max)
└── Sol reviewer (medium)
```

Put the root settings in the project-scoped `.codex/config.toml`, or merge
them into `~/.codex/config.toml` for a personal/global setup:

```toml
model = "gpt-6-sol"
model_reasoning_effort = "medium"

[agents]
enabled = true
max_concurrent_threads_per_session = 4
default_subagent_model = "gpt-6-luna"
default_subagent_reasoning_effort = "max"
```

For the named roles, use these model settings in the corresponding files under
`.codex/agents/`:

```toml
# explorer.toml, worker.toml, tester.toml, researcher.toml
model = "gpt-6-luna"
model_reasoning_effort = "max"
```

```toml
# reviewer.toml
model = "gpt-6-sol"
model_reasoning_effort = "medium"
```

The role files override the inherited `[agents]` defaults. Keep their explicit
`model` pins to preserve this topology. In particular, removing the reviewer's
pin makes it inherit the Luna default. You can remove a `model_reasoning_effort`
override when you want a role to use the configured default effort.

The `plus` profile uses the same model and effort settings. The
`pro-max-2-subagents` and `plus-max-2-subagents` profiles use this same topology
with a concurrent subagent limit of 2 instead of 4.
