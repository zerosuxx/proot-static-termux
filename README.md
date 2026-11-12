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

## Build locally without running the test suite

The PRoot check phase runs a large test suite. To skip it, override
`doCheck` on the exported derivation. Run these commands on a Linux builder
(the flake's package output is Linux-specific; a macOS host needs a configured
Linux builder).

AArch64 Android:

```sh
nix build --impure --expr '
  (builtins.getFlake
    "github:taranarmo/nix-on-droid/62f8ea7c10e2aafe0db512ee8f36d187ac7a56ea"
  ).packages.aarch64-linux.prootTermux-aarch64.overrideAttrs (_: {
    doCheck = false;
  })
' -o result-aarch64
```

x86_64 Android:

```sh
nix build --impure --expr '
  (builtins.getFlake
    "github:taranarmo/nix-on-droid/62f8ea7c10e2aafe0db512ee8f36d187ac7a56ea"
  ).packages.aarch64-linux.prootTermux-x86_64.overrideAttrs (_: {
    doCheck = false;
  })
' -o result-x86_64
```

The binaries are at `result-aarch64/bin/proot-static` and
`result-x86_64/bin/proot-static`.
