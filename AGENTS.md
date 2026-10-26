# Working in this repository

release-please cuts releases from commit messages, so every commit subject starts with a [Conventional Commits](https://www.conventionalcommits.org/) type:

- `fix:` corrects a skill, and releases a patch version.
- `feat:` adds a skill or new behavior to one, and releases a minor version.
- `docs:`, `chore:` and `ci:` change nothing users install, and release nothing.

Never edit the version by hand. It lives in `plugins/epode/.claude-plugin/plugin.json`, `version.txt` and `.release-please-manifest.json`, and merging release-please's release pull request raises all three together.
