# Hermes Plugins

Shareable plugins used with [Hermes Agent](https://github.com/NousResearch/hermes-agent).

Each top-level directory is one plugin package with its own README and example configuration.

## Plugins

| Plugin | Kind | Description |
| --- | --- | --- |
| [`openai-compatible-image`](openai-compatible-image/) | `image_gen` backend | Generic OpenAI-compatible image generation provider with config-driven presets and runtime preset switching. |

## Install

Copy each plugin package into the Hermes runtime path for its plugin type. Image generation backends use:

```text
~/.hermes/plugins/image_gen/<plugin-name>/
```

For `openai-compatible-image`:

```bash
mkdir -p ~/.hermes/plugins/image_gen
cp -R openai-compatible-image ~/.hermes/plugins/image_gen/
```

## Security

This repository should only contain reusable plugin code, documentation, and example configuration. Do **not** commit real API keys, tokens, provider secrets, private URLs, or local runtime state.
