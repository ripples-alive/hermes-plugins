# OpenAI-Compatible Preset Image Provider

A generic Hermes `image_gen` backend for third-party image generation services that expose an OpenAI-compatible `POST /images/generations` API.

The plugin is intentionally provider-neutral:

- no vendor-specific name in the provider identity;
- no hard-coded model, resolution, or quality defaults;
- model/size routing is driven by named presets in `config.yaml`;
- responses are requested as `b64_json` and saved locally, avoiding provider URL accessibility issues;
- `/image_preset` can switch presets globally or for the current gateway session.

## Install

Copy the plugin directory into your Hermes user plugins path:

```bash
mkdir -p ~/.hermes/plugins/image_gen
cp -R openai-compatible-image ~/.hermes/plugins/image_gen/
```

Then enable/select it in `~/.hermes/config.yaml` and restart Hermes or the gateway.

## Configuration

See [`config.example.yaml`](config.example.yaml) for a self-contained example.

Minimal shape:

```yaml
plugins:
  enabled:
    - image_gen/openai-compatible-image

image_gen:
  provider: openai-compatible-image
  preset: fast
  openai_compatible_image:
    base_url: https://your-provider.example/v1
    api_key: ${OPENAI_COMPAT_IMAGE_API_KEY}
    timeout: 240
    presets:
      fast:
        display: Fast default model
        model: provider/model-id
        sizes:
          landscape: 1536x1024
          portrait: 1024x1536
          square: 1024x1024
```

Credential lookup order:

1. `OPENAI_COMPAT_IMAGE_BASE_URL` / `OPENAI_COMPAT_IMAGE_API_KEY` environment variables;
2. `image_gen.openai_compatible_image.base_url` / `api_key`;
3. a configured `custom_providers[]` entry named by `image_gen.openai_compatible_image.custom_provider`;
4. top-level `model.base_url` / `model.api_key` fallback.

Keep real secrets in `~/.hermes/.env` or another secret store, not in public config examples.

## Presets

Each preset must define either a fallback `model`/`size` or per-aspect maps:

```yaml
image_gen:
  openai_compatible_image:
    presets:
      balanced:
        display: Balanced quality/cost
        models:
          landscape: provider/image-landscape
          portrait: provider/image-portrait
          square: provider/image-square
        sizes:
          landscape: 1536x1024
          portrait: 1024x1536
          square: 1024x1024
        extra_body:
          quality: standard
```

`extra_body` is merged into the OpenAI-compatible payload after `model`, `prompt`, `n`, `size`, and `response_format`. The plugin always restores `response_format: b64_json`.

## Runtime command

The plugin registers `/image_preset` (Telegram may also accept `/image-preset` depending on gateway normalization):

- `/image_preset` — show current status and available presets
- `/image_preset list` — list presets
- `/image_preset <preset>` — set a session override
- `/image_preset <preset> --global` — set the global default and current session override
- `/image_preset reset` — clear the current session override

Session override state is stored under `$HERMES_HOME/state/image_preset_overrides.json`.

## Validation

```bash
python3 -m py_compile openai-compatible-image/__init__.py
```
