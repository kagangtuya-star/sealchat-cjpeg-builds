# SealChat cjpeg Builds

Prebuilt standalone `cjpeg` binaries for SealChat, built from a pinned
libjpeg-turbo source revision with GitHub Actions.

## Targets

- Windows AMD64
- Windows ARM64
- Linux AMD64
- Linux ARM64

The Linux builds are intended to be fully static musl binaries. CI rejects a
Linux artifact if the resulting ELF contains a `PT_INTERP` program header or a
`DT_NEEDED` shared-library dependency.

## Upstream

The workflow currently pins libjpeg-turbo 3.2.0 to the exact commit:

`c85e6b905bf237038faa936dab160ebfc5da0344`

The GitHub Actions used by the workflow are also pinned to exact commit SHAs.

## SealChat usage

The primary use case is converting WebP image data to JPEG before embedding
images into DOCX exports.

With Google's `dwebp` and the `cjpeg` built here:

```bash
dwebp input.webp -ppm -o - | cjpeg -quality 85 > output.jpg
```

This avoids writing a large intermediate PNG to disk.

JPEG has no alpha channel. If a WebP image must retain transparency, use a PNG
conversion path instead.

## Linux compatibility

Linux artifacts are built against musl and statically linked, so they do not
depend on the target distribution's glibc version.

The intended compatibility floor is Linux kernel 2.6.39 or newer. For AMD64,
Debian 7+ and CentOS/RHEL 7+ are reasonable supported targets. ARM64 artifacts
are intended for distributions with mature AArch64 kernel/userspace support.

The workflow verifies the final ELF instead of assuming that a target named
`cjpeg-static` is fully standalone.

## Build and verification

Run the workflow manually from:

`Actions -> Build standalone cjpeg -> Run workflow`

Each target produces an Actions artifact containing:

- `cjpeg` or `cjpeg.exe`
- a SHA-256 checksum
- `LICENSE.md` from libjpeg-turbo
- `README.ijg` from libjpeg-turbo

Linux artifacts additionally pass ELF dependency checks before upload.

## Supply-chain policy

- libjpeg-turbo is pinned by exact commit SHA.
- GitHub Actions are pinned by exact commit SHA.
- No prebuilt third-party `cjpeg` binary is downloaded.
- Each binary receives a SHA-256 checksum.
- Upstream licensing files are copied into every build artifact.

## License notice

Generated `cjpeg` binaries remain subject to the licensing terms of
libjpeg-turbo and the Independent JPEG Group. The relevant upstream license
files are included with every artifact.

This repository contains build automation and documentation; add your desired
repository-level license separately if you want to grant explicit reuse rights
for those files.
