# hardened-obscura

A demo repository showing [Chainguard hardened GitHub Actions](https://edu.chainguard.dev/chainguard/actions/overview/)
as drop-in replacements for **obscure community actions** — the long tail of
CI dependencies that rarely get a security review before running with access
to your repository and secrets.

Every third-party action in `.github/workflows/` comes from the
[`chainguard-actions`](https://github.com/orgs/chainguard-actions/repositories)
organization and is referenced by its mutable version tag, with the original
upstream reference preserved as a comment.

## Hardened actions used

| Chainguard action | Upstream | Pinned version | Hardening result | Report |
|---|---|---|---|---|
| `crate-ci-typos` | crate-ci/typos | v1.49.0 | 4 findings fixed: script injection, `$GITHUB_ENV` injection, 14 unpinned `uses:` refs, missing permissions | [HARDENING.md](https://github.com/chainguard-actions/crate-ci-typos/blob/e069b15869a17c6f5d9e2d951c06fcccc248b1a5/HARDENING.md) |
| `andstor-file-existence-action` | andstor/file-existence-action | v3.1.0 | clean pass (0 findings) | [HARDENING.md](https://github.com/chainguard-actions/andstor-file-existence-action/blob/4333b69cff4f5e158117ec7caa497fc0d8c6234f/HARDENING.md) |
| `mikefarah-yq` | mikefarah/yq | v4.53.3 | mutable Docker tag pinned to immutable image digest | [HARDENING.md](https://github.com/chainguard-actions/mikefarah-yq/blob/2b70269397dc80593071c921ebdf559afe5aea7c/HARDENING.md) |
| `chuhlomin-render-template` | chuhlomin/render-template | v1.12 | mutable Docker tag pinned to immutable image digest | [HARDENING.md](https://github.com/chainguard-actions/chuhlomin-render-template/blob/b26f3d75bfa3878230f2a7aa101b39000a7331c1/HARDENING.md) |
| `jlumbroso-free-disk-space` | jlumbroso/free-disk-space | v1.3.1 | 8 high-severity script-injection findings fixed (raw `${{ inputs.* }}` in shell conditions moved to quoted env vars) | [HARDENING.md](https://github.com/chainguard-actions/jlumbroso-free-disk-space/blob/52482bc28020afba6a8cbc838b7ddbd9ab5dedc3/HARDENING.md) |
| `AnimMouse-setup-ffmpeg` | AnimMouse/setup-ffmpeg | v1.2.5 | 6 findings fixed: unpinned refs, `$GITHUB_OUTPUT` injection, unquoted shell expansions | [HARDENING.md](https://github.com/chainguard-actions/AnimMouse-setup-ffmpeg/blob/34ee4b393c648bc7ef4251a66605d68655afad39/HARDENING.md) |
| `actions-checkout` | actions/checkout | v7.0.1 | 7 findings fixed in CI workflows: script injection, unpinned refs, missing permissions | [HARDENING.md](https://github.com/chainguard-actions/actions-checkout/blob/ec419da8013e66e059571719dbfa6d1483438668/HARDENING.md) |

Each report link points at a specific commit of the action; the workflows
reference the corresponding version tag.

## Workflows

Two workflow files run the **same pipeline** — spell check (`typos`),
required-file verification, `yq` release-version extraction, template
rendering, and a media job that frees runner disk space, installs FFmpeg,
and generates/probes a test video — so the before/after can be compared
side by side:

- **`pipeline-upstream.yml`** — the "before": original upstream community
  actions, referenced by mutable tags.
- **`pipeline-hardened.yml`** — the "after": Chainguard hardened
  equivalents, referenced by their version tags with the upstream
  reference preserved as a comment.

## Notes

- Hardened action content lives on **version branches/tags**, not `main`
  (`main` is a metadata stub in most `chainguard-actions` repos), so
  references must use the version tag or, better, the full commit SHA.
- Using these actions requires a Chainguard Actions entitlement:
  `chainctl actions entitlements create --parent $ORGANIZATION`. The actions
  contact Chainguard's licensing/telemetry endpoint at run time.
- If your org restricts allowed actions, add `chainguard-actions/*` to the
  allowlist under *Settings > Actions > General*.
