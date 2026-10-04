# Skemos

A Hyprland rice built around the look of a high-tech schematics / blueprint
terminal — corner brackets, registration marks, schema lines, title-block
framing (think Anduril / Hadrian / Saronic, not e-ink). Motion is fast and
mechanical, not slow fades.

It ships as a reproducible, security-conscious `~/.config`: a single
`install/bootstrap.sh` takes a fresh Arch install to the full desktop, and a
background security stack (firewall, auditd, rootkit/CVE/integrity scanning)
is wired in and surfaced through an in-session panel.

- **`SKEMOS.md`** — running design log: machine specifics, the Lua config
  map, the security stack, and the current state of the rice. Start here to
  understand *why* things are built the way they are.
- **`INSTALL.md`** — step-by-step bare-metal install guide.

## Quick start

```sh
# fresh Arch install: base system, networking, git, a wheel user with sudo —
# see INSTALL.md §0-1 for the exact base-install steps
cd ~
git clone git@github.com:Sebastian-Elstadt/rice-skemos.git ~/.config
cd ~/.config
./install/bootstrap.sh
```

`bootstrap.sh` installs every package in `install/packages.txt`, detects your
GPU (NVIDIA / AMD / Intel) and microcode, sets up the security tooling and
systemd timers, symlinks a few dotfiles outside `~/.config` (`~/.bashrc`,
`~/.bash_profile`), and optionally offers to install Claude Code (see below).
Full details and manual fallbacks are in `INSTALL.md`.

## Layout

```
hypr/       Hyprland config (Lua) — compositor, binds, animations, scripts
waybar/     top bar
rofi/       launcher
mako/       notifications
foot/       kitty/     terminal configs
gtk-3.0/    gtk-4.0/   GTK theme settings
bash/       shell prompt + login banner (sourced from ~/.bashrc)
security/   firewall / auditd / rootkit / CVE / integrity scripts, systemd
            units, sudoers rule, pacman hook, and the SUPER+S panel
install/    bootstrap.sh, the package list, and the Claude Code installer
docs/       design specs and implementation plans for notable features
```

## Security posture

Everything under `security/` and `install/` follows one rule: **be secure,
be contained.**

- Every installed package comes from the official Arch `core`/`extra`
  repos — no AUR, no npm, no curl-pipe-to-shell. (`yay`, `spotify`, `zed`
  are the deliberate, hand-installed exceptions, and are never touched by
  `bootstrap.sh`.)
- A firewall (`ufw`), audit daemon (`auditd`), rootkit scanner
  (`rkhunter`), CVE scanner (`arch-audit`) and a file-integrity checker run
  on systemd timers, root-owned, under a single narrowly-scoped sudoers
  rule. Status and logs are readable from an unprivileged panel
  (`SUPER+S`), never written to by root into user-owned paths.
- The one approved exception is **Claude Code**: an opt-in, prompted
  install (default No) using Anthropic's own installer, never piped
  blindly to a shell. An optional panel action can hand a tool's log to a
  read-only Claude session (no shell, no edit, no web) to get a plain-
  English severity read and recommended next steps — nothing is sent
  without an explicit confirmation.
- A pre-commit hook (`gitleaks`) blocks commits containing secrets.

See `SKEMOS.md` → "Security" for the full design, and `docs/superpowers/`
for the specs and plans behind individual features.

## Machine

Currently tuned for `saber`: NVIDIA RTX 5070 + AMD iGPU, single
2560×1440@180 display. `bootstrap.sh` auto-detects GPU/microcode instead of
hardcoding this, so the same repo should bring up a different machine
correctly — see `SKEMOS.md` for what's been verified and what hasn't.
