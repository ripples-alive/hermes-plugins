# AGENTS.md

Guidance for agents working in this repository.

These instructions combine repository-specific rules with cautious development principles: think before coding, keep changes simple, edit surgically, and verify outcomes.

## Repository purpose

This repository contains shareable Hermes Agent plugins. Keep each plugin package reusable, documented, and safe to publish.

## Development principles

### 1. Think before changing files

Do not assume silently.

- State assumptions when they affect implementation.
- Ask when the task is genuinely ambiguous.
- Surface tradeoffs before choosing a path that changes structure, public API, configuration, or user-facing behavior.
- Push back on unnecessary complexity.
- If a request can be satisfied by documentation or configuration, do not change code.

### 2. Simplicity first

Make the smallest useful change.

- Do not add features beyond the request.
- Do not add abstractions for one-off code.
- Do not add configurability that is not needed.
- Prefer clear, boring code and direct documentation.
- If a solution feels overbuilt, simplify before committing.

### 3. Surgical changes

Touch only what is necessary.

- Do not refactor unrelated code.
- Do not reformat unrelated files or sections.
- Match the existing style of the file being edited.
- Clean up only artifacts created by the current change.
- Mention unrelated issues separately instead of fixing them opportunistically.
- Every changed line should be traceable to the task.

### 4. Goal-driven execution

Turn each task into verifiable outcomes.

For multi-step work, use a short plan in this shape:

```text
1. Change X → verify: Y
2. Change A → verify: B
3. Commit → verify: clean status and pushed branch
```

Keep working until the requested change is complete and verified.

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

Write README content for external users.

Include:

- what the plugin does;
- how to install it;
- how to configure it;
- how to validate it;
- security notes.

Do not include:

- conversation traces;
- internal rationale;
- user feedback history;
- explanations of why a decision was made during development;
- phrasing that reads like a transcript of development discussion.

Convert internal decisions into neutral facts or user-facing instructions.

Avoid wording such as:

- “we decided”;
- “because the user asked”;
- “intentionally” when it mainly explains a naming or design debate;
- “so this repository”;
- “ambiguous” when referring to a prior discussion.

## Security

- Never commit real API keys, tokens, passwords, provider secrets, private URLs, local runtime state, or generated credentials.
- Example configuration must use placeholders or environment variable references.
- Before committing, search for obvious secret patterns and old/local provider names.

## Validation before commit

Run checks that match the files changed. At minimum for the current plugin:

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
            if phrase in text and path.name != 'AGENTS.md':
                print(f'{path}: contains {phrase!r}')
PY
```

Secret/name check:

```bash
git grep -n -E '(sk-[A-Za-z0-9_-]{20,}|gh[pousr]_[A-Za-z0-9_]{20,}|password|secret|token|api_key|Axon|axon)' -- . ':!AGENTS.md'
```

Review matches manually. Placeholder references such as `${OPENAI_COMPAT_IMAGE_API_KEY}` and code paths that read `api_key` are acceptable; real values are not.

## Git workflow

- Keep commits small and descriptive.
- Run validation before committing.
- Do not commit `__pycache__`, `.pyc`, logs, local config, or runtime state.
- Prefer SSH remotes when available.
