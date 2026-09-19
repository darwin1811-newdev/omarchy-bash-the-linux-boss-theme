# BASH the Linux Boss

An [Omarchy](https://omarchy.org/) theme: 60 bash and Linux reference
wallpapers at 3840x2160, basics to pro, on a dark GitHub-style palette, plus a
sci-fi terminal unlock image.

The wallpapers are generic Linux/bash reference — they quote no fact about the
machine that rendered them (no CPU model, kernel version, hostname or
timezone). Every command shown was executed against real output before it
shipped: 920 assertions across four verify scripts. See
[Provenance](#provenance).

## Install

    omarchy theme install https://github.com/darwin1811-newdev/omarchy-bash-the-linux-boss-theme.git

Omarchy clones the repo into `~/.config/omarchy/themes/` and applies it. The
theme name comes from the repo name with a leading `omarchy-` and a trailing
`-theme` stripped, so the directory lands at `bash-the-linux-boss` and the
theme lists as **Bash The Linux Boss**.

Or install it by hand:

    git clone https://github.com/darwin1811-newdev/omarchy-bash-the-linux-boss-theme.git \
      ~/.config/omarchy/themes/bash-the-linux-boss
    omarchy theme set 'Bash The Linux Boss'

Cycle the wallpapers with `omarchy theme bg next`.

## Contents

    colors.toml        the palette every themed app is generated from
    backgrounds/       60 wallpapers, 3840x2160, in reading order
    unlock.png         lock screen image
    preview.png        theme preview (wallpaper)
    preview-unlock.png theme preview (lock screen)
    icons.theme        Yaru-sage-dark
    keyboard.rgb       keyboard backlight colour
    shell.lock.toml    lock screen input colours

Omarchy generates the rest — Hyprland, the terminals, btop, Neovim, VS Code —
from `colors.toml` through its own templates. A theme installed from a git
repo is not allowed to ship those files, because each one runs code or names a
program to launch, so they are deliberately absent here.

## The wallpapers

Roughly in order of depth:

| Range | Covers |
|-------|--------|
| 01-13 | navigation, permissions, variables, tests, branching, loops |
| 14-21 | every bash symbol, functions, arrays, parameter expansion, redirection, printf/read, dates |
| 22-33 | pipes, grep, sed, awk, find, tar, locale, dd, ssh, curl, jq, networking |
| 34-46 | rc files, prompts, tmux, completion, vim, git, users, disks, systemd, pacman, omarchy, keys, containers |
| 47-60 | globbing, strict mode, signals, parallelism, fifos, cron, heredocs, getopts, interactive scripts, patterns, security, portable sh, testing, debugging |

## Provenance

Accuracy was established by execution, not by recall — the verify scripts ran
each command and compared real output. Semantics that cannot be executed come
from the man pages: cron's traps from crontab(5), the PS2/PS4/IFS defaults
from bash(1), unit directives from systemd.unit(5).

Not executed, and why: `bc`, `dig`, `vimtutor`, the container page's flags, the
"fails under dash" half of the Portable sh page, and the bats-core /
shellcheck / checkbashisms lines — those tools were not present on the build
machine. Those pages are still generic and correct; they simply were not run.

Errors the suite caught, for the record:

  * awk's printf ROUNDS 22/7 to 3.143; bc truncates it to 3.142
  * `hostname -I` is a Debian-ism — Arch ships GNU inetutils, where it fails
  * `iconv -c` drops invalid bytes but still exits 1; "silently" was wrong
  * the ACL marker `+` shows in `ls -l`, not in `stat -c %A`
  * GNU date REJECTS 'last day of month'; Jan 31 +1 month is March 3
  * `[ "$n" -gt 0 ]` is safe, but `(( ))`, `$(( ))`, `let`, `declare -i` and
    `[[ -gt ]]` all EXECUTE a `$( )` hidden in an array subscript
  * a flock is held until the LAST process with that fd exits — an inheriting
    background child keeps it alive (start it with `9>&-`)
  * `(( VERBOSE )) && log` as a function body kills a `set -e` script
  * `bash -n` never runs shopt, so it flags `!( )` extglob as a syntax error

## Regenerating the wallpapers

This repository holds only the theme, so that installing it downloads nothing
else. The generator — the page content in a small markup, a Pillow renderer,
and the four verify scripts — lives in its own repository:

> **Source:** https://github.com/darwin1811-newdev/bash-boss

Render there, then copy `out/` into this repository's `backgrounds/`.
