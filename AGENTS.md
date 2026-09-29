# AGENTS.md — pod-kde-desktop

Standalone candy repo for the `kde-desktop` candy — a bare-metal KDE Plasma
desktop with an SDDM display manager that boots a real GPU seat into a graphical
KDE login. The entire candy lives in `charly.yml` at the repo root.

Canonical files:

- `charly.yml` — the `kde-desktop:` candy entity (description, `require`,
  `candy`, `distro`, `service`, `plan`).
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `CHANGELOG/` — per-CalVer history.
- `README.md` — user overview only; never agent guidance.

## Load these skills first (R0)

- `/charly-selkies:kde-shell` — the family skill for the SDDM-free Plasma session
  packages this candy composes (plasma-desktop deps-puller + core components +
  curated apps). Load before editing, building, deploying, or troubleshooting
  this candy.
- `/charly-selkies:selkies-kde-desktop` — the headless streamed KDE flavor
  (composes `kde-shell` directly without SDDM); the contrast that defines this
  candy's workstation-only scope.
- `/charly-pod:pod` — the `kind: pod` / deploy schema reference (this candy is
  composed into a box; tree-position nesting, services).
- `/charly-check:check` — the check/R10 framework: the `check:` step verbs and
  `charly check run <bed>`.
- `/charly-image:layer` — the candy authoring reference (`charly.yml` schema,
  `plan:` step verbs, package sections, service declarations).
- `/charly-internals:git-workflow` — before any git/PR action.

The candy carries no `skill:` entity of its own, and no dedicated `kde-desktop`
skill exists in the corpus; the family skill `/charly-selkies:kde-shell` covers
the shared Plasma session. The gap is routed to the named skill-authoring batch
[opencharly/opencharly#291](https://github.com/opencharly/opencharly/issues/291);
when that lands, add the owning skill here.

## Build / validate / test

- `charly box validate` at the repo root — the structural check: the manifest
  must parse and validate at the installed charly.
- The candy's own `check:` steps are the R10 witness: they assert the `sddm`
  binary and package, the workstation extras (`plasma-systemmonitor`,
  `filelight`, `partitionmanager`, `haruna`), the systemd default target
  `graphical.target`, and the enabled `sddm.service`.
- The merge gate is the **org-wide** `charly/pr-validator` (required check
  `validate / validate`, defined in `opencharly/.github`); this repo has **no**
  per-repo candy gate. Its only workflow file is
  `.github/workflows/tag-on-merge.yml`.

## Modify this repo

- Edit the `kde-desktop:` candy entity in `charly.yml`.
- The SDDM-free Plasma session packages are shared with the headless KDE pod via
  the `layer-kde-shell` dependency; workstation-only extras belong HERE, not in
  `kde-shell`, so the streaming pod stays lean (R3).
- The `sddm` service (`use_packaged: sddm.service`, `scope: system`) and the
  `graphical.target` default are the seat contract; keep them in step.
- Authored under `arch:`; CachyOS inherits arch's package sections via the
  embedded `distro:` vocabulary, so no per-distro duplication.

## Landing

- PR-only. Every change lands through a pull request; the org-required
  `charly/pr-validator` validates the diff and body and arms native auto-merge on
  PASS. Direct pushes to `main` are blocked.
- History lives in `CHANGELOG/` (written by `tag-on-merge` at merge time); the PR
  body IS the changelog.
- The authoritative rulebook is the umbrella `AGENTS.md` in
  `opencharly/opencharly` and `charly/AGENTS.md` in the charly repo. Do not
  restate its rules here.
