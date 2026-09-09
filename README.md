# RSAF private-rclone builder

This repository rebuilds published [RSAF](https://github.com/chenxiaolong/RSAF) releases with `github.com/rclone/rclone` replaced by the private `divyam234/rclone-private` repository.

The workflow runs hourly and can also be started manually for a specific RSAF tag. If the corresponding release tag already exists in this repository, it does nothing.

## Required GitHub Actions secret

Configure this repository secret before running the workflow:

- `PAT_TOKEN`: GitHub token with read access to `divyam234/rclone-private`.

The workflow generates a standard Android debug keystore at build time and uses the usual debug credentials (`android` / `androiddebugkey`) to sign the release APKs.

## What gets published

For each new upstream RSAF release, the workflow:

1. Checks out the exact upstream RSAF release tag and its Go submodule.
2. Checks out `divyam234/rclone-private` from `main` using `PAT_TOKEN`.
3. Adds a local Go module replacement for `github.com/rclone/rclone` in RSAF's `rcbridge` build.
4. Builds RSAF release APKs signed with the generated Android debug key.
5. Publishes those APKs and `SHA256SUMS` under the same tag in this repository.

The release notes record both the RSAF commit and the private rclone commit used for the build.
