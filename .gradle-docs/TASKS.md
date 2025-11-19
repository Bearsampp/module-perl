# Gradle Tasks Reference

Complete reference for all Gradle tasks in the Bearsampp Module Perl project.

---

## Table of Contents

- [Build Tasks](#build-tasks)
- [Verification Tasks](#verification-tasks)
- [Information Tasks](#information-tasks)
- [Task Examples](#task-examples)

---

## Build Tasks

### `release`

Build and package a release for a specific Perl version.

**Group**: `build`

**Usage**:
```bash
# Interactive mode (prompts for version selection)
gradle release

# Non-interactive mode (specify version)
gradle release -PbundleVersion=5.40.0
```

**Parameters**:
- `-PbundleVersion=X.X.X` - Specify the Perl version to build (optional, prompts if not provided)

**Description**:
- Validates the build environment
- Checks if Perl binaries exist in `bin/perl{version}/`
- If not found, downloads from modules-untouched repository
- Prepares the bundle in `bearsampp-build/tmp/bundles_prep/`
- Copies to `bearsampp-build/tmp/bundles_build/` (non-archive version)
- Packages into archive format (7z or zip)
- Generates hash files (MD5, SHA1, SHA256, SHA512)
- Outputs to `bearsampp-build/tools/perl/{bundle.release}/`

**Output**:
```
bearsampp-build/tools/perl/2025.4.26/
├── bearsampp-perl-5.40.0-2025.4.26.7z
├── bearsampp-perl-5.40.0-2025.4.26.7z.md5
├── bearsampp-perl-5.40.0-2025.4.26.7z.sha1
├── bearsampp-perl-5.40.0-2025.4.26.7z.sha256
└── bearsampp-perl-5.40.0-2025.4.26.7z.sha512
```

**Example**:
```bash
# Build Perl 5.40.0
gradle release -PbundleVersion=5.40.0

# Interactive mode
gradle release
# Then select from available versions
```

---

### `releaseAll`

Build and package releases for all available Perl versions in the `bin/` directory.

**Group**: `build`

**Usage**:
```bash
gradle releaseAll
```

**Description**:
- Discovers all versions in `bin/` and `bin/archived/`
- Builds each version sequentially
- Reports success/failure for each version
- Provides summary at the end

**Output**:
```
======================================================================
Build Summary
======================================================================
Total versions: 3
Successful:     3
Failed:         0
[SUCCESS] All versions built successfully!
```

**Example**:
```bash
# Build all versions
gradle releaseAll
```

---

### `clean`

Clean build artifacts and temporary files.

**Group**: `build`

**Usage**:
```bash
gradle clean
```

**Description**:
- Removes Gradle's relocated build directory
- Located at: `bearsampp-build/tmp/gradle/tools/perl/`
- Does not remove final packaged archives

**Example**:
```bash
gradle clean
```

---

## Verification Tasks

### `verify`

Verify the build environment and dependencies.

**Group**: `verification`

**Usage**:
```bash
gradle verify
```

**Description**:
Checks the following:
- Java version (8+)
- `build.properties` file exists
- `bin/` directory exists
- 7-Zip availability (if `bundle.format=7z`)

**Output**:
```
Environment Check Results:
------------------------------------------------------------
  [PASS]     Java 8+
  [PASS]     build.properties
  [PASS]     bin directory
  [PASS]     7-Zip
------------------------------------------------------------

[SUCCESS] All checks passed! Build environment is ready.
```

**Example**:
```bash
gradle verify
```

---

### `validateProperties`

Validate the `build.properties` configuration file.

**Group**: `verification`

**Usage**:
```bash
gradle validateProperties
```

**Description**:
Validates that all required properties are present:
- `bundle.name`
- `bundle.release`
- `bundle.type`
- `bundle.format`

**Output**:
```
[SUCCESS] All required properties are present:
    bundle.name = perl
    bundle.release = 2025.4.26
    bundle.type = tools
    bundle.format = 7z
```

**Example**:
```bash
gradle validateProperties
```

---

## Information Tasks

### `info`

Display build configuration information.

**Group**: `help`

**Usage**:
```bash
gradle info
```

**Description**:
Displays comprehensive build information including:
- Project details
- Bundle properties
- Directory paths
- Java and Gradle versions
- Available task groups

**Output**:
```
================================================================
          Bearsampp Module Perl - Build Info
================================================================

Project:        module-perl
Version:        2025.4.26
Description:    Bearsampp Module - perl

Bundle Properties:
  Name:         perl
  Release:      2025.4.26
  Type:         tools
  Format:       7z

Paths:
  Project Dir:  E:/Bearsampp-development/module-perl
  Root Dir:     E:/Bearsampp-development
  Build Base:   E:/Bearsampp-development/bearsampp-build
  Build Tmp:    E:/Bearsampp-development/bearsampp-build/tmp
  ...

Java:
  Version:      17
  Home:         C:/Program Files/Java/jdk-17

Gradle:
  Version:      8.5
  Home:         C:/Gradle/gradle-8.5
```

**Example**:
```bash
gradle info
```

---

### `listVersions`

List all available Perl versions in the `bin/` and `bin/archived/` directories.

**Group**: `help`

**Usage**:
```bash
gradle listVersions
```

**Description**:
- Scans `bin/` directory for Perl version folders
- Scans `bin/archived/` directory for archived versions
- Displays versions with their location

**Output**:
```
Available perl versions:
------------------------------------------------------------
  5.40.0         [bin]
  5.38.2         [bin]
  5.36.0         [bin/archived]
------------------------------------------------------------
Total versions: 3

To build a specific version:
  gradle release -PbundleVersion=5.40.0
```

**Example**:
```bash
gradle listVersions
```

---

### `listReleases`

List all available Perl releases from the modules-untouched repository.

**Group**: `help`

**Usage**:
```bash
gradle listReleases
```

**Description**:
- Fetches `perl.properties` from modules-untouched repository
- Displays all available versions and their download URLs
- Useful for discovering what versions can be automatically downloaded

**Output**:
```
Available Perl Releases (modules-untouched):
--------------------------------------------------------------------------------
  5.40.0     -> https://github.com/Bearsampp/modules-untouched/releases/...
  5.38.2     -> https://github.com/Bearsampp/modules-untouched/releases/...
  5.36.0     -> https://github.com/Bearsampp/modules-untouched/releases/...
--------------------------------------------------------------------------------
Total releases: 3
```

**Example**:
```bash
gradle listReleases
```

---

### `checkModulesUntouched`

Check the modules-untouched repository integration and available versions.

**Group**: `verification`

**Usage**:
```bash
gradle checkModulesUntouched
```

**Description**:
- Tests connectivity to modules-untouched repository
- Fetches and displays available versions
- Shows version resolution strategy
- Useful for troubleshooting download issues

**Output**:
```
======================================================================
Modules-Untouched Integration Check
======================================================================

Repository URL:
  https://raw.githubusercontent.com/Bearsampp/modules-untouched/main/modules/perl.properties

Fetching perl.properties from modules-untouched...
  [OK] Successfully loaded 15 versions from modules-untouched

======================================================================
Available Versions in modules-untouched
======================================================================
  5.40.0
  5.38.2
  5.36.0
  ...
======================================================================
Total versions: 15

======================================================================
[SUCCESS] modules-untouched integration is working
======================================================================

Version Resolution Strategy:
  1. Check modules-untouched perl.properties (remote)
  2. Construct standard URL format (fallback)
```

**Example**:
```bash
gradle checkModulesUntouched
```

---

## Task Examples

### Example 1: First-Time Setup

```bash
# 1. Verify environment
gradle verify

# 2. List available versions
gradle listVersions

# 3. Build a specific version
gradle release -PbundleVersion=5.40.0
```

---

### Example 2: Building Multiple Versions

```bash
# Build all versions at once
gradle releaseAll

# Or build specific versions one by one
gradle release -PbundleVersion=5.40.0
gradle release -PbundleVersion=5.38.2
gradle release -PbundleVersion=5.36.0
```

---

### Example 3: Troubleshooting

```bash
# Check environment
gradle verify

# Check modules-untouched integration
gradle checkModulesUntouched

# List available releases
gradle listReleases

# Clean and rebuild
gradle clean
gradle release -PbundleVersion=5.40.0
```

---

### Example 4: Using Modules-Untouched

```bash
# Check what versions are available
gradle listReleases

# Build a version that will be downloaded automatically
gradle release -PbundleVersion=5.40.0

# The build will:
# 1. Check if perl.exe exists locally
# 2. If not, download from modules-untouched
# 3. Extract and use for the build
```

---

### Example 5: Debug Mode

```bash
# Run with info logging
gradle release -PbundleVersion=5.40.0 --info

# Run with debug logging
gradle release -PbundleVersion=5.40.0 --debug

# Run with stack traces
gradle release -PbundleVersion=5.40.0 --stacktrace
```

---

### Example 6: CI/CD Pipeline

```bash
# Validate configuration
gradle validateProperties

# Verify environment
gradle verify

# Build all versions
gradle releaseAll

# Check exit code
if [ $? -eq 0 ]; then
  echo "Build successful"
else
  echo "Build failed"
  exit 1
fi
```

---

## Task Dependencies

### Task Execution Order

```
release
├── validateProperties (implicit)
├── verify (implicit)
└── releaseBuild (internal)

releaseAll
├── validateProperties (implicit)
├── verify (implicit)
└── release (for each version)

clean
└── (no dependencies)

verify
└── (no dependencies)

info
└── (no dependencies)

listVersions
└── (no dependencies)

listReleases
└── (no dependencies)

checkModulesUntouched
└── (no dependencies)

validateProperties
└── (no dependencies)
```

---

## Task Properties

### Global Properties

These properties can be set via command line or `gradle.properties`:

| Property              | Description                          | Example                    |
|-----------------------|--------------------------------------|----------------------------|
| `bundleVersion`       | Perl version to build                | `-PbundleVersion=5.40.0`   |
| `BEARSAMPP_BUILD_PATH`| Override build root path (env var)   | Set in environment         |

### build.properties

These properties are read from `build.properties`:

| Property          | Description                          | Default        |
|-------------------|--------------------------------------|----------------|
| `bundle.name`     | Bundle name                          | `perl`         |
| `bundle.release`  | Release version                      | `2025.4.26`    |
| `bundle.type`     | Bundle type                          | `tools`        |
| `bundle.format`   | Archive format                       | `7z`           |
| `build.path`      | Optional: Override build root path   | (none)         |

---

## Task Output Locations

### Temporary Files

| Location                                      | Description                          |
|-----------------------------------------------|--------------------------------------|
| `bearsampp-build/tmp/bundles_prep/tools/perl/` | Prepared bundles before packaging   |
| `bearsampp-build/tmp/bundles_build/tools/perl/` | Non-archive build output           |
| `bearsampp-build/tmp/downloads/perl/`         | Downloaded archives                  |
| `bearsampp-build/tmp/extract/perl/`           | Extracted archives                   |
| `bearsampp-build/tmp/gradle/tools/perl/`      | Gradle build directory               |

### Final Output

| Location                                      | Description                          |
|-----------------------------------------------|--------------------------------------|
| `bearsampp-build/tools/perl/{bundle.release}/` | Final packaged archives and hashes |

---

## Common Task Combinations

### Development Workflow

```bash
# 1. Initial setup
gradle verify
gradle listVersions

# 2. Build and test
gradle release -PbundleVersion=5.40.0

# 3. Clean and rebuild if needed
gradle clean
gradle release -PbundleVersion=5.40.0
```

### Release Workflow

```bash
# 1. Validate configuration
gradle validateProperties

# 2. Verify environment
gradle verify

# 3. Build all versions
gradle releaseAll

# 4. Verify output
ls bearsampp-build/tools/perl/2025.4.26/
```

### Troubleshooting Workflow

```bash
# 1. Check environment
gradle verify

# 2. Check modules-untouched
gradle checkModulesUntouched

# 3. List available versions
gradle listVersions
gradle listReleases

# 4. Clean and retry
gradle clean
gradle release -PbundleVersion=5.40.0 --info
```

---

## Task Performance

### Build Times

Typical build times (approximate):

| Task              | Duration      | Notes                                    |
|-------------------|---------------|------------------------------------------|
| `verify`          | < 1 second    | Quick environment check                  |
| `info`            | < 1 second    | Display information only                 |
| `listVersions`    | < 1 second    | Scan directories                         |
| `listReleases`    | 1-2 seconds   | Network request to modules-untouched     |
| `release`         | 1-5 minutes   | Depends on version size and download     |
| `releaseAll`      | 5-30 minutes  | Depends on number of versions            |
| `clean`           | < 1 second    | Remove build directory                   |

### Optimization Tips

1. **Use Local Binaries**: Place Perl binaries in `bin/` to avoid downloads
2. **Cache Downloads**: Downloaded archives are cached in `bearsampp-build/tmp/downloads/`
3. **Parallel Builds**: Not currently supported, but planned for future
4. **Incremental Builds**: Gradle caching helps with repeated builds

---

**Last Updated**: 2025-04-26  
**Version**: 2025.4.26
