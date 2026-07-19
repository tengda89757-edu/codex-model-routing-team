# Contributing

Open Issues and pull requests in this repository. Keep changes focused on the Codex model-routing Skill and its documentation.

## Pull requests

- Fork the repository and open a pull request against `main`.
- Keep one Skill or one governance concern per pull request.
- Do not include credentials, customer data, personal absolute paths, caches, generated previews, dependencies, reports, or archives.
- Update the Skill Changelog when behavior changes.
- Declare any new network, subprocess, filesystem, or credential capability in the Registry.

## Local validation

```bash
PYTHONDONTWRITEBYTECODE=1 python3 -m unittest discover -s skills/codex-model-routing-team/tests -v
npx --no-install skills add . --list
```

Default CI has no credentials and does not run live-network checks.
