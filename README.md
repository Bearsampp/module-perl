<p align="center"><a href="https://bearsampp.com/contribute" target="_blank"><img width="250" src="img/Bearsampp-logo.svg"></a></p>

[![GitHub release](https://img.shields.io/github/release/bearsampp/module-perl.svg?style=flat-square)](https://github.com/bearsampp/module-perl/releases/latest)
![Total downloads](https://img.shields.io/github/downloads/bearsampp/module-perl/total.svg?style=flat-square)

This is a module of [Bearsampp project](https://github.com/bearsampp/bearsampp) involving Perl.

## Documentation and downloads

https://bearsampp.com/module/perl

## Build (Gradle)

The build system has been modernized to align with the Bruno module and now writes artifacts to the shared bearsampp-build directory instead of the local project build/ folder.
Additionally, Gradle's own temporary `build/` directory is relocated to the shared tmp area so nothing is written under the module folder during builds.

Ant build deprecated:
- The former Ant build is no longer supported for this module. The `build.xml` file remains only as a stub that instructs users to run Gradle tasks.

Prerequisites:
- Java 8+
- Gradle (system Gradle only; do not use Gradle wrapper in this repository)
- 7-Zip installed and available in PATH or via `7Z_HOME` when using `bundle.format=7z`

Build properties are defined in `build.properties`:
- `bundle.name` (e.g., `perl`)
- `bundle.release` (e.g., `2025.4.26`)
- `bundle.type` (e.g., `tools`)
- `bundle.format` (`7z` or `zip`)
- Optional: `build.path` to override the default build root

Build output location:
- Default root: `<repo-root>/../bearsampp-build`
- You can override with:
  - `build.path` in `build.properties`, or
  - Environment variable `BEARSAMPP_BUILD_PATH`

Within the build root, artifacts are organized as:
`{buildRoot}/{bundle.type}/{bundle.name}/{bundle.release}/bearsampp-{name}-{version}-{release}.{7z|zip}`

Archive layout:
- The archive contains a single top-level folder named `{name}{version}` (e.g., `perl5.40.0`) that includes all files. This mirrors the Bruno module behavior.

Gradle internal build directory (relocated):
`{buildRoot}/tmp/gradle/{bundle.type}/{bundle.name}`

Common tasks (use system Gradle `gradle` command):
- List tasks: `gradle tasks`
- Show build info: `gradle info`
- Verify environment: `gradle verify`
- List available versions from `bin/` and `bin/archived/`: `gradle listVersions`
- Build a specific version: `gradle release -PbundleVersion=5.40.0`
- Build all available versions in `bin*/`: `gradle releaseAll`
- Clean Gradle project artifacts: `gradle clean`

Notes:
- Place source bundles under `bin/` as directories named like `perl5.40.0` (or in `bin/archived/`). The build will package these and produce checksums (MD5, SHA1, SHA256, SHA512).
- When `bundle.format` is `7z`, ensure 7-Zip is installed. You can set `7Z_HOME` to the folder containing `7z.exe`.
 - The produced 7z/zip preserves the version folder as the archive root (e.g., `perl5.40.0/…`).

## Issues

Issues must be reported on [Bearsampp repository](https://github.com/bearsampp/bearsampp/issues).
