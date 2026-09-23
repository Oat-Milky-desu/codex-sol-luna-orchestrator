# Plus Plan

The `plus` installer choice uses the shared profile topology: GPT-6 Sol at
`medium` reasoning for the root and reviewer, and GPT-6 Luna at `max` reasoning
for execution roles and default subagents. It has a concurrency limit of 4,
matching `pro`.

The installers (`setup.sh`, `setup.ps1`) ask for your plan and install this
profile automatically when you select `Plus`. Setup copies
`profiles/plus/codex/` to `.codex/` and `profiles/plus/agents/` to `.agents/`
without rewriting configuration. For manual installation, copy those folders
and the repository's `AGENTS.md` to the target.

For a global setup, merge `profiles/plus/codex/config.toml` into:

`~/.codex/config.toml`

```toml
# Root
model = "gpt-6-sol"
model_reasoning_effort = "medium"

[agents]
enabled = true
max_concurrent_threads_per_session = 4
default_subagent_model = "gpt-6-luna"
default_subagent_reasoning_effort = "max"
```

Subagents keep their pinned models from `.codex/agents/*.toml`. Explorer,
worker, tester, and researcher explicitly set `model = "gpt-6-luna"` and
`model_reasoning_effort = "max"`. The reviewer stays on GPT-6 Sol at
`medium` effort. Its separate context provides an independent review of the change implemented by
Luna execution agents and integrated by the Sol root.

See `token-usage.md` for how to measure usage on your own tasks.

For global installation, also copy `profiles/plus/codex/agents/` to
`~/.codex/agents/` and `profiles/plus/agents/skills/sol-orchestrator/` to
`~/.agents/skills/sol-orchestrator/`. Use the skill from the same profile
as the configuration so its model and reasoning instructions match. The
`plus-max-2-subagents` profile has the same model and effort settings, with a
concurrent subagent limit of 2.
