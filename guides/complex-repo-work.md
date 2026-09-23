# Complex Repository Work

Choose this preset for architecture changes and difficult debugging when you
prefer the standard service tier. The project's shared reasoning settings
remain Sol `medium` and Luna `max`.

This preset changes only the service tier; it does not raise reasoning effort
or alter any installed role settings.

Add or merge this into:

`~/.codex/config.toml`

```toml
model = "gpt-6-sol"
model_reasoning_effort = "medium"
service_tier = "standard"
```

If your Codex version does not support `service_tier`, remove that line and
keep the model and reasoning settings.
