# hardened-obscura

A demo repository showing [Chainguard hardened GitHub Actions](https://edu.chainguard.dev/chainguard/actions/overview/)
as drop-in replacements for **obscure community actions** — the long tail of
CI dependencies that rarely get a security review before running with access
to your repository and secrets.

Every third-party action in `.github/workflows/` comes from the
[`chainguard-actions`](https://github.com/orgs/chainguard-actions/repositories)
organization and is pinned to an immutable commit SHA, with the original
upstream tag preserved as a comment per the Chainguard migration guidance.

## Hardened actions used

| Chainguard action | Upstream | Pinned version | Hardening result |
|---|---|---|---|
| `crate-ci-typos` | crate-ci/typos | v1.49.0 | 4 findings fixed: script injection, `$GITHUB_ENV` injection, 14 unpinned `uses:` refs, missing permissions |
| `andstor-file-existence-action` | andstor/file-existence-action | v3.1.0 | clean pass (0 findings) |
| `mikefarah-yq` | mikefarah/yq | v4.53.3 | mutable Docker tag pinned to immutable image digest |
| `chuhlomin-render-template` | chuhlomin/render-template | v1.12 | mutable Docker tag pinned to immutable image digest |
| `jlumbroso-free-disk-space` | jlumbroso/free-disk-space | v1.3.1 | 8 high-severity script-injection findings fixed (raw `${{ inputs.* }}` in shell conditions moved to quoted env vars) |
| `AnimMouse-setup-ffmpeg` | AnimMouse/setup-ffmpeg | v1.2.5 | 6 findings fixed: unpinned refs, `$GITHUB_OUTPUT` injection, unquoted shell expansions |
| `carlosperate-download-file-action` | carlosperate/download-file-action | v2.0.3 | clean pass (0 findings) |
| `actions-checkout` | actions/checkout | v7.0.1 | 7 findings fixed in CI workflows: script injection, unpinned refs, missing permissions |

## Workflows

- **`ci.yml`** — spell-checks the repo (`typos`), verifies required files
  exist, reads the release version out of `config/release.yaml` with `yq`,
  and renders `templates/report.md.tpl` into a build report.
- **`media.yml`** — frees runner disk space, installs FFmpeg, downloads a
  sample video, and probes/transcodes it.

## Notes

- Hardened action content lives on **version branches/tags**, not `main`
  (`main` is a metadata stub in most `chainguard-actions` repos), so
  references must use the version tag or, better, the full commit SHA.
- Using these actions requires a Chainguard Actions entitlement:
  `chainctl actions entitlements create --parent $ORGANIZATION`. The actions
  contact Chainguard's licensing/telemetry endpoint at run time.
- If your org restricts allowed actions, add `chainguard-actions/*` to the
  allowlist under *Settings > Actions > General*.
