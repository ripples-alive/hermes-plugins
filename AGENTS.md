# AGENTS.md

Guidance for agents working in this repository.

## Repository purpose

This repository contains shareable Hermes Agent plugins. Keep each plugin package reusable, documented, and safe to publish.

## Structure

- Each top-level plugin directory is one plugin package.
- Plugin-specific documentation and example configuration belong inside that plugin directory.
- Runtime installation paths may differ from the public repository layout. Document install commands clearly instead of mirroring runtime paths unnecessarily.

Current package:

```text
openai-compatible-image/
  README.md
  __init__.py
  plugin.yaml
  config.example.yaml
```

## Public documentation standards

- Write README content for external users.
- Include what the plugin does, how to install it, how to configure it, how to validate it, and security notes.
- Do not include conversation traces, internal rationale, user feedback history, or explanations of why a decision was made during development.
- Convert internal decisions into neutral facts or user-facing instructions.
- Avoid wording such as “we decided”, “because the user asked”, “intentionally”, “so this repository”, or other phrasing that reads like a transcript of the development discussion.

## Security

- Never commit real API keys, tokens, passwords, provider secrets, private URLs, local runtime state, or generated credentials.
- Example configuration must use placeholders or environment variable references.
- Before committing, search for obvious secret patterns and old/local provider names.

Suggested checks:

```bash
git diff --check
python3 -m py_compile openai-compatible-image/__init__.py
```

Documentation hygiene check:

```bash
python3 - <<'PY'
from pathlib import Path
bad = [
    'conversation', 'decision', 'because the user', 'we decided',
    'so this repository', 'intentionally', 'ambiguous',
]
for path in Path('.').rglob('*'):
    if path.is_file() and path.suffix in {'.md', '.yaml', '.yml'} and '.git' not in path.parts:
        text = path.read_text(errors='ignore')
        for phrase in bad:
            if phrase in text:
                print(f'{path}: contains {phrase!r}')
PY
```

Secret/name check:

```bash
git grep -n -E '(sk-[A-Za-z0-9_-]{20,}|gh[pousr]_[A-Za-z0-9_]{20,}|password|secret|token|api_key|Axon|axon)' -- . ':!AGENTS.md'
```

Review any matches manually; placeholders such as `${OPENAI_COMPAT_IMAGE_API_KEY}` are acceptable.

## Git workflow

- Keep commits small and descriptive.
- Run validation before committing.
- Do not commit `__pycache__`, `.pyc`, logs, local config, or runtime state.
