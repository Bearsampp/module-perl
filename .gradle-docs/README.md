# Bearsampp Module Perl - Gradle Build Documentation

## Table of Contents

- [Overview](#overview)
- [Quick Start](#quick-start)
- [Installation](#installation)
- [Build Tasks](#build-tasks)
- [Configuration](#configuration)
- [Architecture](#architecture)
- [Troubleshooting](#troubleshooting)

---

## Overview

The Bearsampp Module Perl project uses a **pure Gradle build system**. This provides:

- **Modern Build System**     - Native Gradle tasks and conventions
- **Better Performance**       - Incremental builds and caching
- **Simplified Maintenance**   - Pure Groovy/Gradle DSL
- **Enhanced Tooling**         - IDE integration and dependency management
- **Cross-Platform Support**   - Works on Windows, Linux, and macOS
- **Modules-Untouched Integration** - Automatic download from modules-untouched repository

### Project Information

| Property          | Value                                    |
|-------------------|------------------------------------------|
| **Project Name**  | module-perl                              |
| **Group**         | com.bearsampp.modules                    |
| **Type**          | Perl Module Builder                      |
| **Build Tool**    | Gradle 8.x+                              |
| **Language**      | Groovy (Gradle DSL)                      |

---

## Quick Start

### Prerequisites

| Requirement       | Version       | Purpose                                  |
|-------------------|---------------|------------------------------------------|
| **Java**          | 8+            | Required for Gradle execution            |
| **Gradle**        | 8.0+          | Build automation tool                    |
| **7-Zip**         | Latest        | Archive compression (when using 7z format) |

### Basic Commands

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

---

## Installation

### 1. Clone the Repository

```bash
git clone https://github.com/bearsampp/module-perl.git
cd module-perl
```

### 2. Verify Environment

```bash
gradle verify
```

This will check:
- Java version (8+)
- Required files (build.properties)
- Directory structure (bin/)
- Build dependencies

### 3. List Available Versions

```bash
gradle listVersions
```

### 4. Build Your First Release

```bash
# Interactive mode (prompts for version)
gradle release

# Or specify version directly
gradle release -PbundleVersion=5.40.0
```

---

## Build Tasks

### Core Build Tasks

| Task                  | Description                                      | Example                                  |
|-----------------------|--------------------------------------------------|------------------------------------------|
| `release`             | Build and package release (interactive/non-interactive) | `gradle release -PbundleVersion=5.40.0` |
| `releaseAll`          | Build all available versions in bin/             | `gradle releaseAll`                      |
| `clean`               | Clean build artifacts and temporary files        | `gradle clean`                           |

### Verification Tasks

| Task                      | Description                                  | Example                                      |
|---------------------------|----------------------------------------------|----------------------------------------------|
| `verify`                  | Verify build environment and dependencies    | `gradle verify`                              |
| `validateProperties`      | Validate build.properties configuration      | `gradle validateProperties`                  |

### Information Tasks

| Task                | Description                                      | Example                    |
|---------------------|--------------------------------------------------|----------------------------|
| `info`              | Display build configuration information          | `gradle info`              |
| `listVersions`      | List available bundle versions in bin/           | `gradle listVersions`      |
| `listReleases`      | List all available releases from modules-untouched | `gradle listReleases`  |
| `checkModulesUntouched` | Check modules-untouched integration          | `gradle checkModulesUntouched` |

### Task Groups

| Group            | Purpose                                          |
|------------------|--------------------------------------------------|
| **build**        | Build and package tasks                          |
| **verification** | Verification and validation tasks                |
| **help**         | Help and information tasks                       |

---

## Configuration

### build.properties

The main configuration file for the build:

```properties
bundle.name     = perl
bundle.release  = 2025.4.26
bundle.type     = tools
bundle.format   = 7z
```

| Property          | Description                          | Example Value  |
|-------------------|--------------------------------------|----------------|
| `bundle.name`     | Name of the bundle                   | `perl`         |
| `bundle.release`  | Release version                      | `2025.4.26`    |
| `bundle.type`     | Type of bundle                       | `tools`        |
| `bundle.format`   | Archive format                       | `7z` or `zip`  |
| `build.path`      | Optional: Override build root path   | `C:/Bearsampp-build` |

### gradle.properties

Gradle-specific configuration:

```properties
# Gradle daemon configuration
org.gradle.daemon=true
org.gradle.parallel=true
org.gradle.caching=true

# JVM settings
org.gradle.jvmargs=-Xmx2g -XX:MaxMetaspaceSize=512m
```

### Directory Structure

```
module-perl/
├── .gradle-docs/          # Gradle documentation
│   ├── README.md          # Main documentation
│   ├── TASKS.md           # Task reference
│   ├── CONFIGURATION.md   # Configuration guide
│   └── API.md             # API reference
├── bin/                   # Perl version bundles
│   ├── perl5.40.0/
│   ├── perl5.38.2/
│   └── archived/          # Archived versions
│       └── perl5.36.0/
├── build.gradle           # Main Gradle build script
├── settings.gradle        # Gradle settings
├── build.properties       # Build configuration
└── gradle.properties      # Gradle-specific settings

bearsampp-build/                    # External build directory (outside repo)
├── tmp/                            # Temporary build files
│   ├── bundles_prep/tools/perl/    # Prepared bundles
│   ├── bundles_build/tools/perl/   # Build staging
│   ├── downloads/perl/             # Downloaded dependencies
│   ├── extract/perl/               # Extracted archives
│   └── gradle/tools/perl/          # Gradle build directory
└── tools/perl/                     # Final packaged archives
    └── 2025.4.26/                  # Release version
        ├── bearsampp-perl-5.40.0-2025.4.26.7z
        ├── bearsampp-perl-5.40.0-2025.4.26.7z.md5
        ├── bearsampp-perl-5.40.0-2025.4.26.7z.sha1
        ├── bearsampp-perl-5.40.0-2025.4.26.7z.sha256
        └── bearsampp-perl-5.40.0-2025.4.26.7z.sha512
```

---

## Architecture

### Build Process Flow

```
1. User runs: gradle release -PbundleVersion=5.40.0
                    ↓
2. Validate environment and version
                    ↓
3. Check if perl.exe exists in bin/perl5.40.0/
                    ↓
4. If not found, download from modules-untouched:
   - Fetch perl.properties from modules-untouched
   - Download perl-5.40.0-win64.7z
   - Extract to tmp/extract/perl/5.40.0/
                    ↓
5. Create preparation directory (tmp/bundles_prep/)
                    ↓
6. Copy Perl files to prep directory
   - Preserves version folder: perl5.40.0/
                    ↓
7. Copy to bundles_build directory (non-archive version)
                    ↓
8. Package prepared folder into archive
   - Location: bearsampp-build/tools/perl/{bundle.release}/
   - Archive includes top-level folder: perl5.40.0/
                    ↓
9. Generate hash files (MD5, SHA1, SHA256, SHA512)
```

### Modules-Untouched Integration

The build system integrates with the [modules-untouched repository](https://github.com/Bearsampp/modules-untouched) to automatically download Perl binaries when they're not found locally.

**Version Resolution Strategy**:
1. Check if perl.exe exists in `bin/perl{version}/`
2. If not found, check cached extract in `bearsampp-build/tmp/extract/perl/{version}/`
3. If still not found, download from modules-untouched:
   - Fetch `perl.properties` from modules-untouched repository
   - Look up download URL for the version
   - Download and extract the archive
   - Use extracted binaries for the build

**perl.properties Format**:
```properties
5.40.0=https://github.com/Bearsampp/modules-untouched/releases/download/perl-5.40.0/perl-5.40.0-win64.7z
5.38.2=https://github.com/Bearsampp/modules-untouched/releases/download/perl-5.38.2/perl-5.38.2-win64.7z
```

**Fallback URL Pattern**:
If a version is not found in perl.properties, the build system constructs a standard URL:
```
https://github.com/Bearsampp/modules-untouched/releases/download/perl-{version}/perl-{version}-win64.7z
```

### Packaging Details

- **Archive name format**: `bearsampp-perl-{version}-{bundle.release}.{7z|zip}`
- **Location**: `bearsampp-build/tools/perl/{bundle.release}/`
  - Example: `bearsampp-build/tools/perl/2025.4.26/bearsampp-perl-5.40.0-2025.4.26.7z`
- **Content root**: The top-level folder inside the archive is `perl{version}/` (e.g., `perl5.40.0/`)
- **Structure**: The archive contains the Perl version folder at the root with all Perl files inside

**Archive Structure Example**:
```
bearsampp-perl-5.40.0-2025.4.26.7z
└── perl5.40.0/              ← Version folder at root
    ├── perl.exe
    ├── perl5.40.0.exe
    ├── bin/
    ├── lib/
    ├── site/
    └── ...
```

**Verification Commands**:

```bash
# List archive contents with 7z
7z l bearsampp-build/tools/perl/2025.4.26/bearsampp-perl-5.40.0-2025.4.26.7z | more

# You should see entries beginning with:
#   perl5.40.0/perl.exe
#   perl5.40.0/bin/
#   perl5.40.0/lib/
#   perl5.40.0/...

# Extract and inspect with PowerShell (zip example)
Expand-Archive -Path bearsampp-build/tools/perl/2025.4.26/bearsampp-perl-5.40.0-2025.4.26.zip -DestinationPath .\_inspect
Get-ChildItem .\_inspect\perl5.40.0 | Select-Object Name

# Expected output:
#   perl.exe
#   perl5.40.0.exe
#   bin/
#   lib/
#   ...
```

**Hash Files**: Each archive is accompanied by hash sidecar files:
- `.md5` - MD5 checksum
- `.sha1` - SHA-1 checksum
- `.sha256` - SHA-256 checksum
- `.sha512` - SHA-512 checksum

Example:
```
bearsampp-build/tools/perl/2025.4.26/
├── bearsampp-perl-5.40.0-2025.4.26.7z
├── bearsampp-perl-5.40.0-2025.4.26.7z.md5
├── bearsampp-perl-5.40.0-2025.4.26.7z.sha1
├── bearsampp-perl-5.40.0-2025.4.26.7z.sha256
└── bearsampp-perl-5.40.0-2025.4.26.7z.sha512
```

---

## Troubleshooting

### Common Issues

#### Issue: "Dev path not found"

**Symptom:**
```
Dev path not found: E:/Bearsampp-development/dev
```

**Solution:**
This is a warning only. The dev path is optional for most tasks. If you need it, ensure the `dev` project exists in the parent directory.

---

#### Issue: "Bundle version not found"

**Symptom:**
```
Bundle version not found: E:/Bearsampp-development/module-perl/bin/perl5.99.0
```

**Solution:**
1. List available versions: `gradle listVersions`
2. Use an existing version: `gradle release -PbundleVersion=5.40.0`
3. Or let the build download from modules-untouched (if available)

---

#### Issue: "Failed to download from modules-untouched"

**Symptom:**
```
Failed to download from modules-untouched: Connection refused
```

**Solution:**
1. Check internet connectivity
2. Verify the version exists: `gradle listReleases`
3. Check modules-untouched integration: `gradle checkModulesUntouched`
4. Manually download and extract to `bin/perl{version}/`

---

#### Issue: "perl.exe not found"

**Symptom:**
```
perl.exe not found at E:/Bearsampp-development/module-perl/bin/perl5.40.0/perl.exe
```

**Solution:**
1. Ensure Perl binaries are in the correct directory structure
2. The build will attempt to download from modules-untouched automatically
3. Or manually place Perl binaries in `bin/perl{version}/`

---

#### Issue: "Java version too old"

**Symptom:**
```
Java 8+ required
```

**Solution:**
1. Check Java version: `java -version`
2. Install Java 8 or higher
3. Update JAVA_HOME environment variable

---

#### Issue: "7-Zip not found"

**Symptom:**
```
7-Zip not found. Please install 7-Zip or set 7Z_HOME environment variable.
```

**Solution:**
1. Install 7-Zip from https://www.7-zip.org/
2. Or set `7Z_HOME` environment variable to 7-Zip installation directory
3. Or change `bundle.format` to `zip` in build.properties

---

### Debug Mode

Run Gradle with debug output:

```bash
gradle release -PbundleVersion=5.40.0 --info
gradle release -PbundleVersion=5.40.0 --debug
```

### Clean Build

If you encounter issues, try a clean build:

```bash
gradle clean
gradle release -PbundleVersion=5.40.0
```

## Additional Resources

- [Gradle Documentation](https://docs.gradle.org/)
- [Bearsampp Project](https://github.com/bearsampp/bearsampp)
- [Modules-Untouched Repository](https://github.com/Bearsampp/modules-untouched)
- [Perl Downloads](https://www.perl.org/get.html)

---

## Support

For issues and questions:

- **GitHub Issues**: https://github.com/bearsampp/module-perl/issues
- **Bearsampp Issues**: https://github.com/bearsampp/bearsampp/issues
- **Documentation**: https://bearsampp.com/module/perl

---

**Last Updated**: 2025-04-26  
**Version**: 2025.4.26  
**Build System**: Pure Gradle (no wrapper)

**Notes**:
- This project deliberately does not ship the Gradle Wrapper. Install Gradle 8+ locally and run with `gradle ...`.
- The build system integrates with modules-untouched for automatic binary downloads.
