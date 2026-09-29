# AGENTS.md — layer-docker-ce

Standalone candy repo for the `docker-ce` layer — the Docker CE engine, the
containerd runtime, and the buildx/compose plugins from Docker's own
repositories. The candy lives in `charly.yml` at the repo root: the per-distro
package + repo arms, the `iptable_nat` `run:` step, the `check:` assertions, and
the embedded `skill:` entity projected into the marketplace corpus as
`/charly-coder:docker-ce`.

Canonical files:

- `charly.yml` — the `docker-ce:` candy entity and the `docker-ce-skill:` skill
  entity.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — user overview only; never agent guidance.

## Load these skills first (R0)

- `/charly-coder:docker-ce` — the owning skill. The per-distro Docker repos, the
  package set, and the `iptable_nat` boot-module step. Load before editing or
  troubleshooting the layer.
- `/charly-image:layer` — the candy authoring reference (`charly.yml` schema,
  `plan:` step verbs incl. `check:`/`run:`, per-distro and distro-version tag
  sections, package/repo blocks). Load before editing any entity field or plan
  step.

## Build / validate / test

- The merge gate is the **org-wide** `charly/pr-validator` (required check
  `validate / validate`, defined in `opencharly/.github`); this repo has **no
  per-repo candy gate** and ships only `.github/workflows/tag-on-merge.yml`.
- The candy's `plan:` `check:` steps are the functional evidence: the `docker`
  and `containerd` binaries plus their version banners, `docker compose version`,
  the `iptable_nat` modules-load drop-in, and the package-installed checks with
  their `package_map:`. They must stay valid on every distro arm they run on.
- The apt repo URL differs per codename (`debian:13` → `trixie`, `ubuntu:24.04` →
  `noble`), so each distro-version tag section declares its own `repo:` block.

## Modify this repo

- Edit the `docker-ce:` candy entity AND the `docker-ce-skill:` skill entity in
  `charly.yml` together. The skill is the projected usage source, so a package,
  repo, or behaviour change not mirrored in the skill leaves the corpus stale.
- Keep the `iptable_nat` boot-module step: without it the Docker bridge cannot
  NAT, and the package checks would still pass — so it is asserted separately.
- New behaviour claims belong in the `plan:` as an observable `check:` step, and
  in the skill body.

## Landing

- PR-only. Every change lands through a pull request; the org-required
  `charly/pr-validator` validates the diff and body and arms native auto-merge
  on PASS. Direct pushes to `main` are blocked.
- History lives in `CHANGELOG/` (written by `tag-on-merge` at merge time); the
  PR body IS the changelog.
- The authoritative rulebook is the umbrella `AGENTS.md` in
  `opencharly/opencharly` and `charly/AGENTS.md` in the charly repo. Do not
  restate its rules here.
