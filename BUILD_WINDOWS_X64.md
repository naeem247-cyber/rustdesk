# Windows x64 build

This fork initially tracks the RustDesk upstream `master` branch without
functional or branding changes. The first verified build used upstream commit
`807e05ea9a7e298ed2deb438195faaafce19cdd2` (2026-07-30).

## Verified configuration

- Runner: GitHub-hosted `windows-2022`
- Rust target: `x86_64-pc-windows-msvc`
- Rust toolchain: `1.75`
- Flutter: `3.24.5` (stable, x64)
- LLVM: `15.0.6`
- vcpkg commit: `120deac3062162151622ca4860575a33844ba10b`
- vcpkg triplet: `x64-windows-static`
- Build features: portable Flutter client, hardware codec support, VRAM support
- Build command: `python3 .\build.py --portable --flutter --skip-portable-pack --hwcodec --vram`

These values come from `.github/workflows/flutter-build.yml`; use the versions
committed with the source instead of substituting newer tools.

## Reproduce with GitHub Actions

1. Fork `rustdesk/rustdesk` and enable Actions on the fork.
2. Open **Actions > Flutter Nightly Build**.
3. Select **Run workflow**, choose the `master` branch, and start the run.
4. Wait for `build-for-windows-flutter / x86_64-pc-windows-msvc` to succeed.
5. Download the `rustdesk-unsigned-windows-x86_64` artifact from the run.

The workflow generates Flutter/Rust bridge files, builds the supporting
`RustDeskTempTopMostWindow` binary, installs the vcpkg manifest dependencies,
builds the release client, and adds the upstream virtual-display and printer
driver files. The artifact is unsigned because signing secrets are intentionally
not present in the fork.

## Local equivalent

A local build requires Visual Studio 2022 with Desktop development with C++,
Git, Python 3, LLVM 15.0.6, Rust 1.75, Flutter 3.24.5, and vcpkg at the pinned
commit above. Initialize submodules, configure `VCPKG_ROOT`, install the
manifest for `x64-windows-static`, apply the repository's Flutter 3.24 patch
and custom x64 engine as shown in `.github/workflows/flutter-build.yml`, then
run the build command from the repository root.

## Verification record

- Source commit: `807e05ea9a7e298ed2deb438195faaafce19cdd2`
- GitHub Actions run: <https://github.com/naeem247-cyber/rustdesk/actions/runs/30687402675>
- Windows x64 build job: the compile, unsigned-artifact upload,
  self-extracting EXE, and MSI steps succeeded. The subsequent attempt to
  publish a `nightly` GitHub release returned `403` because the fork workflow
  token did not have release-write permission; this did not affect the build
  artifact.
- Artifact: `rustdesk-unsigned-windows-x86_64.zip` (31.6 MB in GitHub Actions)
- Artifact SHA-256:
  `5ce3e7627ec15ffc883b62630082080c92051e44986ef80a6ad926e58ea1f155`
- Extracted executable: `rustdesk.exe`, version `1.4.9+67`
- PE machine: `0x8664` (`IMAGE_FILE_MACHINE_AMD64`, Windows x86-64)
