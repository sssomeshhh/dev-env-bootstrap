# dev-env-bootstrap

A single Bash script that bootstraps a 25-tool development environment on a fresh macOS or Debian/Ubuntu machine.

**Supported platforms:** macOS (Apple Silicon) and Debian / Ubuntu.

## Quick start

```bash
git clone https://github.com/sssomeshhh/dev-env-bootstrap.git ~/dev-env-bootstrap
cd ~/dev-env-bootstrap

# Optional: generate a starter config with your overrides
./bootstrap --init-config
$EDITOR ~/.config/dev-env/config

# Review effective config, then install
./bootstrap

# Or skip the confirmation prompt
./bootstrap --yes
```

After install, start a new shell (or `source ~/.config/dev-env/env.sh` in the current one) to pick up `PATH` and per-dep env fragments.

## What gets installed

25 tools across 7 categories:

- **VCS / identity:** `git`, `gh`, `gpg`, `ssh`, `hf`
- **C/C++ toolchain:** `ccache`, `cmake`, `ninja`, `boost`, `llvm` (clang + lld)
- **JVM:** Adoptium JDK 21 + 25, GraalVM 25, `gradle`, `maven`
- **Other languages:** `golang`, `node`, `rust` (rustup), `python` (uv)
- **Container / k8s:** `docker`, `kubernetes` (kubectl), `helm`
- **LLM serving:** `ollama`, `vllm`
- **OS prereqs:** `base`, `sys`, `bash` (modern bash on macOS)

Run `./bootstrap --help` for the full configuration table — 23 `DEVENV_*` variables you can override in `~/.config/dev-env/config`.

## Design

- **Idempotent.** Re-runnable; existing installs are no-op'd. `_pkg` uses brew on macOS / apt on Linux; `_tarball` uses version markers; rc-block and ssh-config-include use marker blocks.
- **Manifest-driven config.** All 23 `DEVENV_*` defaults live in a single bash-array manifest at the top of `bootstrap`. The `--help` table, `--init-config` template, and pre-install effective-config preview all derive from it. A startup drift-lint catches manifest gaps before any work runs.
- **Confirmation prompt.** Shows the effective config before installing; pass `--yes` to skip.
- **No secrets in the repo.** The script writes git identity and imports GPG/SSH keys only if you set the relevant `DEVENV_GIT_*` / `DEVENV_*_KEY_PATH` variables in your config; key material itself lives in your own files.
- **OS-symmetric where possible** (`uv`, `hf`, `gradle` use the same install path on both OSes). OS-asymmetric where install paths genuinely differ (`ollama` via brew on Mac vs systemd installer on Linux; `kubectl` via brew on Mac vs `pkgs.k8s.io` apt repo on Linux; etc.).

## Configuration

Three CLI flags:

| Flag | Effect |
|---|---|
| (none) | Show effective config table, prompt y/N, then install all 25 deps. |
| `--yes` / `-y` | Skip the prompt; install non-interactively. |
| `--init-config` | Write a starter `~/.config/dev-env/config` template (refuses to overwrite without `--force`). |
| `--help` / `-h` | Print this script's header doc + the full configuration variables table. |

All overrides go in `~/.config/dev-env/config` (sourced by `bootstrap` on each run if present). Only set the variables you want to change from the defaults — everything else inherits from the manifest.

## License

MIT — see `LICENSE`.
