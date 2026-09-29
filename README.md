# docker-ce

Docker CE engine, the containerd runtime, and the buildx/compose plugins.

The `docker-ce` candy installs the upstream Docker Community Edition stack — the
`docker` CLI + `dockerd` engine, the `containerd` runtime, and the `buildx` and
`compose` plugins — from Docker's own repositories (the `docker` metapackage on
Arch). It also queues the `iptable_nat` kernel module for load at boot so the
Docker bridge network can NAT. Every artifact is a fixed on-disk path or a CLI
that reports its version, so the install is directly verifiable.

## What it provides

| Property | Value |
|---|---|
| Layer / candy | `docker-ce` |
| Binaries | `/usr/bin/docker`, `/usr/bin/containerd` |
| Plugins | `docker buildx`, `docker compose` |
| Config | `/etc/modules-load.d/ip_tables.conf` (`iptable_nat`) |
| Service / port | none (engine not started by the candy) |

Per-distro packages, all from Docker's own repos:

- `arch` — `containerd`, `docker`, `docker-buildx`, `docker-compose`.
- `fedora` — `containerd.io`, `docker-ce`, `docker-ce-cli`, `docker-buildx-plugin`,
  `docker-compose-plugin` (from `docker-ce-stable`).
- `debian-13` / `ubuntu-24.04` — the same package set, each with its own apt repo
  (the URL differs per codename: `trixie` vs `noble`).

## How to use it

Compose the layer by pinning this repo in a box's `candy:` list:

```yaml
my-docker-box:
  candy:
    base: fedora
    candy:
      - '@github.com/opencharly/layer-docker-ce:v2026.239.1626'
```

Then, inside the built image:

```bash
docker --version
containerd --version
docker compose version
grep iptable_nat /etc/modules-load.d/ip_tables.conf
```

## Layout

- `charly.yml` — the `docker-ce:` candy entity (the per-distro package + repo
  arms, the `iptable_nat` `run:` step, the `check:` assertions) and the embedded
  `docker-ce-skill:` skill entity.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — this user overview.

## Related

- Owning skill: `/charly-coder:docker-ce`
- Alternative: `/charly-distros:container-nesting` — the rootless podman-based
  nested container recipe
- Siblings: `/charly-coder:kubernetes-layer`, `/charly-coder:github-actions`
- [`opencharly/charly`](https://github.com/opencharly/charly) — the charly CLI and image builder
- [`opencharly/opencharly`](https://github.com/opencharly/opencharly) — the umbrella
