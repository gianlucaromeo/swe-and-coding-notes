# Most common commit "types"

- **feat**: a new feature. Something end-users or developers can now use.
- **fix**: a bug fix. Code changes that solve an issue.
- **chore**: maintenance tasks that do not affect functionality (updating dependencies, configs, build scripts).
- **docs**: documentation only (README, comments, guides).
- **style**: formatting, white-space, indentation, commas, etc. No code meaning changes.
- **refactor**: code restructuring without changing external behavior (cleanup, better patterns).
- **perf**: performance improvements (reduce complexity, faster execution).
- **test**: add or modify tests only.
- **build**: changes to build system, CI/CD, package manager, or external tooling.
- **ci**: changes to continuous integration configuration and scripts (GitHub Actions, Travis, etc.).
- **revert**: revert a previous commit. Usually generated automatically by `git revert`.

### Less common but sometimes used

- **security**: patches addressing security vulnerabilities.
- **deps**: specifically dependency updates (sometimes included under chore).
- **wip**: work in progress (shouldn’t be merged to main, but used in some flows).