# RSAF private-rclone builder

This repository rebuilds published [RSAF](https://github.com/chenxiaolong/RSAF) releases with `github.com/rclone/rclone` replaced by the private `divyam234/rclone-private` repository.

The workflow runs hourly and can also be started manually for a specific RSAF tag. If the corresponding release tag already exists in this repository, it does nothing.

## Required GitHub Actions secrets

Configure these repository secrets before running the workflow:

- `RCLONE_REPO_TOKEN`: GitHub token with read access to `divyam234/rclone-private`.
- `RSAF_RELEASE_KEYSTORE_BASE64`: Base64-encoded Android release keystore.
- `RSAF_RELEASE_KEYSTORE_PASSPHRASE`: Keystore password.
- `RSAF_RELEASE_KEY_ALIAS`: Signing key alias.
- `RSAF_RELEASE_KEY_PASSPHRASE`: Signing key password.

For the keystore secret, encode the file as a single-line base64 value, for example:

```bash
base64 -w0 rsaf-release.jks
```

Keep the same signing key for every release so installed builds can be upgraded in place.

## What gets published

For each new upstream RSAF release, the workflow:

1. Checks out the exact upstream RSAF release tag and its Go submodule.
2. Checks out `divyam234/rclone-private` from `main` using `RCLONE_REPO_TOKEN`.
3. Adds a local Go module replacement for `github.com/rclone/rclone` in RSAF's `rcbridge` build.
4. Builds RSAF's signed release APKs.
5. Publishes those APKs and `SHA256SUMS` under the same tag in this repository.

The release notes record both the RSAF commit and the private rclone commit used for the build.
