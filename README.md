# platform

The clearcutt-showcase platform team's repository: what every app builds and runs on,
built and published with [clearcutt-factory](https://github.com/northcutted/clearcutt-factory).
Part of the [ClearCutt showcase](https://github.com/northcutted/clearcutt-showcase).

| Path | What it is | Published as |
|---|---|---|
| `images/{build,run}-{go,python,node}/` | Build (toolchain) and run images: manifest, lock, Containerfile. Shared settings in `images/_shared/` | `ghcr.io/northcutted/clearcutt-showcase/platform/<name>:<line>` |
| `stacks/{go,python,node}.yaml` | How to build each kind of app: build image, steps, run image | `ghcr.io/northcutted/clearcutt-showcase/stacks/<name>:1`, signed by `stacks.yml` |
| `org/profile.yaml`, `org/trust.yaml`, `org/vex/` | The shared org profile every app repository extends, the trust policy (who signs images and stacks) that factory and clearcutt-verify share, and the security team's VEX statements | `ghcr.io/northcutted/clearcutt-showcase/factory/org:1`, signed by `profile.yml` |
| `factory.org.yaml` | This repository's own profile: extends `org/profile.yaml` by path, adding the registry | |

Workflows: `platform.yml` (pull requests gate; `main` publishes images, then
stacks and the profile, then notifies the repositories with the topic
`clearcutt-app`), `update-locks.yml` (weekly lock updates, or on demand).

## Open findings and who owns them

Every finding the estate reports has an owner here, so none sits unread.
The [portal](https://northcutted.github.io/clearcutt-showcase/) is the live list; this table says who acts and
what closes each.

| Finding | Where | Owner | Why it is open | What closes it |
|---|---|---|---|---|
| Six High advisories in npm's bundled packages (brace-expansion 5.0.8, ip-address 10.2.0, undici 6.27.0, http-cache-semantics 4.2.0), most with a fixed version upstream | `build-node` only: npm runs in the build stage and doesn't ship (run-node and storefront report none of them) | Platform team (`images/build-node`, see CODEOWNERS) | Wolfi's `npm` package bundles these versions; update-locks re-resolves the lock weekly (it last moved build-node in platform PR #4) | A Wolfi `npm` rebuild with the fixed versions: the next weekly update-locks PR picks it up. If it lingers past 30 days, pin npm from its own release instead |
| CVE-2025-15367 (CPython `poplib`, Medium, no fix in Wolfi yet) | `run-python`, `build-python`, `catalog` | Security (`org/vex/`) | No fixed `python-3.14` package yet | For catalog: `org/vex/catalog.openvex.json` (not affected: catalog never imports `poplib`). For the bases: the fixed package, through update-locks |
