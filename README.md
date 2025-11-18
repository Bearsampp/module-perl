<p align="center"><a href="https://bearsampp.com/contribute" target="_blank"><img width="250" src="img/Bearsampp-logo.svg"></a></p>

[![GitHub release](https://img.shields.io/github/release/bearsampp/module-perl.svg?style=flat-square)](https://github.com/bearsampp/module-perl/releases/latest)
![Total downloads](https://img.shields.io/github/downloads/bearsampp/module-perl/total.svg?style=flat-square)

This is a module of [Bearsampp project](https://github.com/bearsampp/bearsampp) involving Perl.

## Build System

This project uses **Gradle** as its build system with a pure Gradle implementation (no wrapper).

### Quick Start

```bash
# Display build information
gradle info

# List all available tasks
gradle tasks

# Verify build environment
gradle verify

# Build a release (interactive)
gradle release

# Build a specific version (non-interactive)
gradle release -PbundleVersion=5.40.0

# Build all versions
gradle releaseAll

# Clean build artifacts
gradle clean
```

### Prerequisites

| Requirement       | Version       | Purpose                                  |
|-------------------|---------------|------------------------------------------|
| **Java**          | 8+            | Required for Gradle execution            |
| **Gradle**        | 8.0+          | Build automation tool                    |
| **7-Zip**         | Latest        | Archive compression (when using 7z format) |

### Available Tasks

| Task                  | Description                                      |
|-----------------------|--------------------------------------------------|
| `release`             | Build release package (interactive/non-interactive) |
| `releaseAll`          | Build all available versions in bin/             |
| `clean`               | Clean build artifacts and temporary files        |
| `verify`              | Verify build environment and dependencies        |
| `info`                | Display build configuration information          |
| `listVersions`        | List available bundle versions in bin/           |
| `listReleases`        | List releases from modules-untouched             |
| `checkModulesUntouched` | Check modules-untouched integration            |
| `validateProperties`  | Validate build.properties configuration          |

For complete documentation, see [.gradle-docs/README.md](.gradle-docs/README.md)

## Documentation

- **Build Documentation**: [.gradle-docs/README.md](.gradle-docs/README.md)
- **Task Reference**: [.gradle-docs/TASKS.md](.gradle-docs/TASKS.md)
- **Configuration Guide**: [.gradle-docs/CONFIGURATION.md](.gradle-docs/CONFIGURATION.md)
- **API Reference**: [.gradle-docs/API.md](.gradle-docs/API.md)
- **Module Downloads**: https://bearsampp.com/module/perl

## Issues

Issues must be reported on [Bearsampp repository](https://github.com/bearsampp/bearsampp/issues).
