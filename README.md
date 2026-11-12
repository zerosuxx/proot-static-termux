# proot-static-termux

Builds the Termux-compatible static PRoot binaries used by Nix-on-Droid and
publishes them as GitHub Release assets for AArch64 and x86_64.

The source is pinned to the `taranarmo/nix-on-droid` commit that updates
`proot-termux` to `unstable-2026-10-12`, including its `fchmodat2` translation.
The build runs on an x86_64 Linux GitHub Actions runner and cross-compiles both
architectures with the upstream Nix derivations. The workflow applies a small
local patch to that pinned source to skip LLVM's failing regression-test phase;
LLVM itself is still built normally.

## Release

Push a version tag such as `v2026.10.12` to build and publish:

- `proot-static-aarch64`
- `proot-static-x86_64`

The release assets are raw static binaries. Verify the downloaded file with
SHA-256 before staging it as `/bin/.proot-static.new` on a Nix-on-Droid device.
