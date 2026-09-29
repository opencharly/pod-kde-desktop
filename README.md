# pod-kde-desktop

The `kde-desktop` candy of the OpenCharly candy library, as a standalone repo
(kind-prefixed naming). It ships a bare-metal KDE Plasma desktop with an SDDM
display manager that boots a real GPU seat into a graphical KDE login.

## What it provides

A bare-metal KDE Plasma desktop for a graphical workstation (host or VM guest
with a real GPU output). It composes the shared `kde-shell` layer (the
SDDM-free Plasma session: plasma-desktop deps-puller + core components + curated
apps) and adds the display-manager half: SDDM plus the workstation-only extras
that would only bloat the headless streaming pod (system monitor, browser
integration, firewall/thunderbolt KCMs, kwallet, kdeconnect, bluetooth, partition
tools, media players). The layer sets the systemd default to `graphical.target`
so a real GPU output lights up a KDE login.

| Property | Value |
|---|---|
| Service | `sddm` (`use_packaged: sddm.service`, `enable: true`, `scope: system`) |
| Requires | `pod-dbus`, `pod-pipewire` |
| Candy | `layer-kde-shell` (the SDDM-free Plasma session packages) |
| Packages | `sddm` plus the workstation extras (`plasma-systemmonitor`, `filelight`, `partitionmanager`, `kdeconnect`, `haruna`, …) |
| Target | `graphical.target` (systemd default) |

Only meaningful on a systemd target with a real display (bare host / VM guest
with GPU). On supervisord-init containers the `sddm` `use_packaged` entry is
skipped (warn) by design. For the headless streamed KDE pod use `selkies-kde-desktop`,
which composes `kde-shell` directly without SDDM.

## How to use it

```bash
charly box build kde-desktop
charly config kde-desktop
charly start kde-desktop
```

The candy's own `plan:` checks assert the `sddm` binary and package, the
workstation extras (`plasma-systemmonitor`, `filelight`, `partitionmanager`,
`haruna`), the systemd default target `graphical.target`, and the enabled
`sddm.service`.

## Layout

- `charly.yml` — the `kde-desktop:` candy entity (description, `require`,
  `candy`, `distro`, `service`, `plan`).
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `CHANGELOG/` — per-Calver history.
- `README.md` — this user overview.

## Related

- Owning skill family: `/charly-selkies:kde-shell` — the SDDM-free Plasma session
  packages this candy shares with the headless KDE pod.
- `/charly-selkies:selkies-kde-desktop` — the headless streamed KDE flavor
  (no SDDM, no `graphical.target`).
- `/charly-selkies:kde-selkies` — the KDE nested-compositor primitive for the
  selkies streaming desktop.
- [`opencharly/charly`](https://github.com/opencharly/charly) — the charly CLI and image builder
- [`opencharly/opencharly`](https://github.com/opencharly/opencharly) — the umbrella
