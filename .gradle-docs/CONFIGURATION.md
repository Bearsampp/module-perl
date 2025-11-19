# Configuration Guide

Complete configuration reference for the Bearsampp Module Perl build system.

---

## Table of Contents

- [Configuration Files](#configuration-files)
- [Build Properties](#build-properties)
- [Gradle Properties](#gradle-properties)
- [Environment Variables](#environment-variables)
- [Directory Configuration](#directory-configuration)
- [Perl Version Configuration](#perl-version-configuration)
- [Archive Configuration](#archive-configuration)
- [Best Practices](#best-practices)

---

## Configuration Files

### Overview

| File                  | Purpose                                  | Required |
|-----------------------|------------------------------------------|----------|
| `build.properties`    | Main build configuration                 | Yes      |
| `gradle.properties`   | Gradle-specific settings                 | No       |
| `settings.gradle`     | Gradle project settings                  | Yes      |
| `releases.properties` | Release metadata                         | No       |

---

## Build Properties

### File: `build.properties`

Main configuration file for the build system.

**Location**: `<project-root>/build.properties`

**Format**: Java properties file

**Example**:
```properties
bundle.name = perl
bundle.release = 2025.4.26
bundle.type = tools
bundle.format = 7z

#build.path = C:/Bearsampp-build
```

### Property Reference

#### `bundle.name`

**Description**: Name of the bundle

**Type**: String

**Required**: Yes

**Default**: `perl`

**Example**:
```properties
bundle.name = perl
```

**Notes**:
- Used in archive naming: `bearsampp-{bundle.name}-{version}-{bundle.release}.{format}`
- Used in directory structure: `bearsampp-build/{bundle.type}/{bundle.name}/`

---

#### `bundle.release`

**Description**: Release version identifier

**Type**: String (date format recommended: YYYY.M.D)

**Required**: Yes

**Default**: None

**Example**:
```properties
bundle.release = 2025.4.26
```

**Notes**:
- Used in archive naming
- Used in output directory structure
- Recommended format: `YYYY.M.D` (e.g., `2025.4.26`)
- Can be any string, but date format is conventional

---

#### `bundle.type`

**Description**: Type of bundle (determines output directory)

**Type**: String

**Required**: Yes

**Default**: `tools`

**Valid Values**: `tools`, `bins`, `apps`

**Example**:
```properties
bundle.type = tools
```

**Notes**:
- Determines output path: `bearsampp-build/{bundle.type}/{bundle.name}/`
- For Perl, use `tools`
- Matches Bearsampp module categorization

---

#### `bundle.format`

**Description**: Archive format for packaged releases

**Type**: String

**Required**: Yes

**Default**: `7z`

**Valid Values**: `7z`, `zip`

**Example**:
```properties
bundle.format = 7z
```

**Notes**:
- `7z`: Better compression, requires 7-Zip installed
- `zip`: Native Java support, no external dependencies
- Affects archive extension: `.7z` or `.zip`

---

#### `build.path`

**Description**: Override default build root path

**Type**: String (file path)

**Required**: No

**Default**: `<project-parent>/bearsampp-build`

**Example**:
```properties
build.path = C:/Bearsampp-build
```

**Notes**:
- Optional property
- Overrides default build location
- Can also be set via `BEARSAMPP_BUILD_PATH` environment variable
- Priority: `build.path` > `BEARSAMPP_BUILD_PATH` > default

---

## Gradle Properties

### File: `gradle.properties`

Gradle-specific configuration for build performance and behavior.

**Location**: `<project-root>/gradle.properties`

**Format**: Java properties file

**Example**:
```properties
# Gradle daemon configuration
org.gradle.daemon=true
org.gradle.parallel=true
org.gradle.caching=true

# JVM settings
org.gradle.jvmargs=-Xmx2g -XX:MaxMetaspaceSize=512m

# Gradle console output
org.gradle.console=auto
```

### Property Reference

#### Daemon Configuration

```properties
org.gradle.daemon=true
```

**Description**: Enable Gradle daemon for faster builds

**Recommended**: `true`

---

#### Parallel Execution

```properties
org.gradle.parallel=true
```

**Description**: Enable parallel task execution

**Recommended**: `true`

**Notes**: Currently limited benefit for this project, but useful for future enhancements

---

#### Build Cache

```properties
org.gradle.caching=true
```

**Description**: Enable Gradle build cache

**Recommended**: `true`

**Notes**: Speeds up repeated builds

---

#### JVM Settings

```properties
org.gradle.jvmargs=-Xmx2g -XX:MaxMetaspaceSize=512m
```

**Description**: JVM memory settings for Gradle

**Recommended**: `-Xmx2g -XX:MaxMetaspaceSize=512m`

**Notes**:
- `-Xmx2g`: Maximum heap size (2GB)
- `-XX:MaxMetaspaceSize=512m`: Metaspace limit
- Adjust based on available system memory

---

## Environment Variables

### `BEARSAMPP_BUILD_PATH`

**Description**: Override default build root path

**Type**: String (file path)

**Required**: No

**Example**:
```bash
# Windows (PowerShell)
$env:BEARSAMPP_BUILD_PATH = "C:/Bearsampp-build"

# Windows (CMD)
set BEARSAMPP_BUILD_PATH=C:\Bearsampp-build

# Linux/macOS
export BEARSAMPP_BUILD_PATH=/opt/bearsampp-build
```

**Priority**: Lower than `build.path` in `build.properties`

---

### `7Z_HOME`

**Description**: Path to 7-Zip installation directory

**Type**: String (directory path)

**Required**: No (only if `bundle.format=7z` and 7-Zip not in PATH)

**Example**:
```bash
# Windows (PowerShell)
$env:7Z_HOME = "C:/Program Files/7-Zip"

# Windows (CMD)
set 7Z_HOME=C:\Program Files\7-Zip
```

**Notes**:
- Only needed if 7-Zip is not in PATH
- Should point to directory containing `7z.exe`
- Build will search common locations automatically

---

### `JAVA_HOME`

**Description**: Path to Java installation directory

**Type**: String (directory path)

**Required**: Yes (usually set by Java installer)

**Example**:
```bash
# Windows (PowerShell)
$env:JAVA_HOME = "C:/Program Files/Java/jdk-17"

# Windows (CMD)
set JAVA_HOME=C:\Program Files\Java\jdk-17

# Linux/macOS
export JAVA_HOME=/usr/lib/jvm/java-17-openjdk
```

---

## Directory Configuration

### Default Directory Structure

```
<project-root>/
├── bin/                   # Perl version bundles
│   ├── perl5.40.0/
│   ├── perl5.38.2/
│   └── archived/          # Archived versions
│       └── perl5.36.0/
├── build.gradle
├── build.properties
└── gradle.properties

<build-root>/              # Default: <project-parent>/bearsampp-build
├── tmp/                   # Temporary build files
│   ├── bundles_prep/tools/perl/
│   ├── bundles_build/tools/perl/
│   ├── downloads/perl/
│   ├── extract/perl/
│   └── gradle/tools/perl/
└── tools/perl/            # Final output
    └── 2025.4.26/
        └── bearsampp-perl-5.40.0-2025.4.26.7z
```

### Customizing Build Root

**Method 1: build.properties**
```properties
build.path = C:/Custom-Build-Path
```

**Method 2: Environment Variable**
```bash
$env:BEARSAMPP_BUILD_PATH = "C:/Custom-Build-Path"
```

**Method 3: Command Line** (not currently supported, use methods 1 or 2)

---

## Perl Version Configuration

### Directory Structure

Each Perl version should be placed in the `bin/` directory with the following structure:

```
bin/
└── perl5.40.0/            # Version folder
    ├── perl.exe           # Main Perl executable (required)
    ├── perl5.40.0.exe     # Version-specific executable
    ├── bin/               # Perl binaries
    ├── lib/               # Perl libraries
    ├── site/              # Site-specific modules
    └── ...
```

### Version Naming Convention

**Format**: `perl{major}.{minor}.{patch}`

**Examples**:
- `perl5.40.0`
- `perl5.38.2`
- `perl5.36.0`

**Notes**:
- Version folder name must start with `perl`
- Must contain `perl.exe` in the root or subdirectory
- Build system will search for `perl.exe` recursively

---

### Archived Versions

Older versions can be moved to `bin/archived/`:

```
bin/
├── perl5.40.0/            # Current versions
├── perl5.38.2/
└── archived/              # Archived versions
    ├── perl5.36.0/
    └── perl5.34.0/
```

**Notes**:
- Archived versions are still available for building
- Helps organize current vs. legacy versions
- `gradle listVersions` shows both locations

---

### Modules-Untouched Integration

If Perl binaries are not found locally, the build system will attempt to download from modules-untouched:

**URL Pattern**:
```
https://github.com/Bearsampp/modules-untouched/releases/download/perl-{version}/perl-{version}-win64.7z
```

**Configuration**: No configuration needed, automatic

**Cache Location**: `bearsampp-build/tmp/downloads/perl/`

**Extract Location**: `bearsampp-build/tmp/extract/perl/{version}/`

---

## Archive Configuration

### Archive Naming

**Format**: `bearsampp-{bundle.name}-{version}-{bundle.release}.{format}`

**Example**: `bearsampp-perl-5.40.0-2025.4.26.7z`

**Components**:
- `bearsampp`: Fixed prefix
- `{bundle.name}`: From `build.properties` (e.g., `perl`)
- `{version}`: Perl version (e.g., `5.40.0`)
- `{bundle.release}`: From `build.properties` (e.g., `2025.4.26`)
- `{format}`: From `build.properties` (e.g., `7z` or `zip`)

---

### Archive Structure

**Top-Level Folder**: `perl{version}/`

**Example**:
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

**Notes**:
- Archive always contains the version folder at the root
- Matches structure of other Bearsampp modules
- Ensures consistent extraction behavior

---

### Hash Files

Each archive is accompanied by hash sidecar files:

**Generated Hashes**:
- `.md5` - MD5 checksum
- `.sha1` - SHA-1 checksum
- `.sha256` - SHA-256 checksum
- `.sha512` - SHA-512 checksum

**Format**:
```
{hash} {filename}
```

**Example** (`.md5` file):
```
a1b2c3d4e5f6... bearsampp-perl-5.40.0-2025.4.26.7z
```

---

## Best Practices

### 1. Version Organization

**Recommended**:
```
bin/
├── perl5.40.0/            # Latest stable
├── perl5.38.2/            # Previous stable
└── archived/
    ├── perl5.36.0/        # Older versions
    └── perl5.34.0/
```

**Benefits**:
- Clear separation of current vs. archived versions
- Easy to find latest versions
- Maintains build capability for all versions

---

### 2. Build Path Configuration

**Recommended**: Use environment variable for team consistency

```bash
# Set in team documentation or CI/CD
$env:BEARSAMPP_BUILD_PATH = "C:/Bearsampp-build"
```

**Benefits**:
- Consistent across team members
- No need to modify `build.properties`
- Easy to change for different environments

---

### 3. Archive Format Selection

**Recommended**: Use `7z` for production, `zip` for development

**Production** (`build.properties`):
```properties
bundle.format = 7z
```

**Development** (`build.properties`):
```properties
bundle.format = zip
```

**Benefits**:
- `7z`: Better compression for distribution
- `zip`: Faster builds, no external dependencies

---

### 4. Gradle Properties

**Recommended** (`gradle.properties`):
```properties
org.gradle.daemon=true
org.gradle.parallel=true
org.gradle.caching=true
org.gradle.jvmargs=-Xmx2g -XX:MaxMetaspaceSize=512m
org.gradle.console=auto
```

**Benefits**:
- Faster builds with daemon and caching
- Better memory management
- Improved console output

---

### 5. Version Control

**Recommended**: `.gitignore` configuration

```gitignore
# Build outputs (external directory)
/bearsampp-build/

# Gradle files
/.gradle/
/build/

# IDE files
/.idea/
*.iml
/.vscode/

# Temporary files
*.tmp
*.log
```

**Benefits**:
- Keeps repository clean
- Avoids committing build artifacts
- Consistent across team

---

### 6. CI/CD Configuration

**Recommended**: Environment-specific configuration

```yaml
# Example GitHub Actions
env:
  BEARSAMPP_BUILD_PATH: ${{ github.workspace }}/build-output
  JAVA_HOME: ${{ env.JAVA_HOME_17_X64 }}
```

**Benefits**:
- Consistent builds across environments
- Easy to configure per environment
- No hardcoded paths

---

## Configuration Examples

### Example 1: Development Setup

**build.properties**:
```properties
bundle.name = perl
bundle.release = 2025.4.26
bundle.type = tools
bundle.format = zip
```

**gradle.properties**:
```properties
org.gradle.daemon=true
org.gradle.caching=true
org.gradle.jvmargs=-Xmx1g
```

**Notes**: Fast builds with zip format, moderate memory usage

---

### Example 2: Production Setup

**build.properties**:
```properties
bundle.name = perl
bundle.release = 2025.4.26
bundle.type = tools
bundle.format = 7z
build.path = C:/Bearsampp-build
```

**gradle.properties**:
```properties
org.gradle.daemon=true
org.gradle.parallel=true
org.gradle.caching=true
org.gradle.jvmargs=-Xmx2g -XX:MaxMetaspaceSize=512m
```

**Notes**: Optimized compression, dedicated build path, maximum performance

---

### Example 3: CI/CD Setup

**build.properties**:
```properties
bundle.name = perl
bundle.release = 2025.4.26
bundle.type = tools
bundle.format = 7z
# build.path set via environment variable
```

**Environment Variables**:
```bash
BEARSAMPP_BUILD_PATH=/opt/ci-build
JAVA_HOME=/usr/lib/jvm/java-17-openjdk
```

**Notes**: Environment-driven configuration, no hardcoded paths

---

## Troubleshooting Configuration

### Issue: Build path not writable

**Symptom**: Permission denied errors

**Solution**:
1. Check directory permissions
2. Use a different build path
3. Run with appropriate permissions

---

### Issue: 7-Zip not found

**Symptom**: `7-Zip not found` error

**Solution**:
1. Install 7-Zip
2. Set `7Z_HOME` environment variable
3. Or change `bundle.format` to `zip`

---

### Issue: Invalid property value

**Symptom**: Build fails with property error

**Solution**:
1. Run `gradle validateProperties`
2. Check property format in `build.properties`
3. Ensure all required properties are present

---

**Last Updated**: 2025-04-26  
**Version**: 2025.4.26
