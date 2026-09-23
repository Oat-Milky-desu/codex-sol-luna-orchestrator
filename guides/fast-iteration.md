# Fast Iteration

Choose this preset when latency matters and you want Sol to orchestrate
quickly with Luna subagents.

The shared profile already uses Sol `medium` for orchestration and review,
with Luna `max` for execution. This preset keeps those reasoning settings and
changes only the service tier to `fast`.

Add or merge this into:

`~/.codex/config.toml`

```toml
model = "gpt-6-sol"
model_reasoning_effort = "medium"
service_tier = "fast"
```

If your Codex version does not support `service_tier`, remove that line and
keep the model and reasoning settings.
