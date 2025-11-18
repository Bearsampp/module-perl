# Documentation Index

Complete index of all Gradle build documentation for Bearsampp Module Perl.

---

## Quick Links

| Document              | Description                                      | Link                          |
|-----------------------|--------------------------------------------------|-------------------------------|
| **Main Documentation**| Complete build system guide                      | [README.md](README.md)        |
| **Task Reference**    | All available Gradle tasks                       | [TASKS.md](TASKS.md)          |
| **Configuration**     | Configuration files and properties               | [CONFIGURATION.md](CONFIGURATION.md) |
| **API Reference**     | Build script API and helper functions            | [API.md](API.md)              |

---

## Documentation Structure

```
.gradle-docs/
├── INDEX.md              # This file - Documentation index
├── README.md             # Main documentation and quick start
├── TASKS.md              # Complete task reference
├── CONFIGURATION.md      # Configuration guide
└── API.md                # API reference for build scripts
```

---

## Getting Started

### New Users

1. **Start Here**: [README.md](README.md) - Overview and quick start
2. **Verify Setup**: Run `gradle verify` to check environment
3. **List Tasks**: Run `gradle tasks` to see available tasks
4. **Build Release**: Run `gradle release -PbundleVersion=5.40.0`

### Advanced Users

1. **Task Reference**: [TASKS.md](TASKS.md) - All tasks with examples
2. **Configuration**: [CONFIGURATION.md](CONFIGURATION.md) - Advanced configuration
3. **API Reference**: [API.md](API.md) - Build script API and extensions
4. **Custom Tasks**: Create custom tasks using API reference

---

## Quick Reference

### Essential Commands

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

### Essential Files

| File                  | Purpose                                  |
|-----------------------|------------------------------------------|
| `build.gradle`        | Main Gradle build script                 |
| `settings.gradle`     | Gradle project settings                  |
| `build.properties`    | Build configuration                      |
| `gradle.properties`   | Gradle-specific settings                 |
| `releases.properties` | Release metadata                         |

### Essential Directories

| Directory             | Purpose                                  |
|-----------------------|------------------------------------------|
| `bin/`                | Perl version bundles                     |
| `bin/archived/`       | Archived Perl versions                   |
| `bearsampp-build/tmp/` | Temporary build files (external)       |
| `bearsampp-build/tools/perl/` | Final packaged archives (external) |
| `.gradle-docs/`       | Gradle documentation                     |

---

## Support

For documentation issues or questions:

- **GitHub Issues**: https://github.com/bearsampp/module-perl/issues
- **Bearsampp Issues**: https://github.com/bearsampp/bearsampp/issues
- **Documentation**: This directory (.gradle-docs/)

---

**Last Updated**: 2025-04-26  
**Version**: 2025.4.26  
**Total Documents**: 5
