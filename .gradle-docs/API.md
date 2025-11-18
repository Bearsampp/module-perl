# API Reference

API reference for the Bearsampp Module Perl build system.

---

## Table of Contents

- [Build Script API](#build-script-api)
- [Helper Functions](#helper-functions)
- [Extension Points](#extension-points)
- [Properties API](#properties-api)
- [Task API](#task-api)

---

## Build Script API

### Overview

The build script (`build.gradle`) provides a comprehensive API for building and packaging Perl modules. This document describes the internal APIs and extension points available for customization.

---

## Helper Functions

### `findPerlDirectory(File path)`

Find the directory containing `perl.exe` within a given path.

**Parameters**:
- `path` (File): Root directory to search

**Returns**: File - Directory containing `perl.exe`, or null if not found

**Description**:
- Searches recursively for `perl.exe` or `Perl.exe`
- Uses breadth-first search algorithm
- Returns first match found

**Example**:
```groovy
def perlDir = findPerlDirectory(file("bin/perl5.40.0"))
if (perlDir) {
    println "Found Perl at: ${perlDir}"
}
```

---

### `find7ZipExecutable()`

Locate the 7-Zip executable on the system.

**Parameters**: None

**Returns**: String - Path to `7z.exe`, or null if not found

**Description**:
- Checks `7Z_HOME` environment variable
- Searches common installation paths
- Uses `where` command on Windows

**Search Order**:
1. `$7Z_HOME/7z.exe`
2. `C:/Program Files/7-Zip/7z.exe`
3. `C:/Program Files (x86)/7-Zip/7z.exe`
4. `D:/Program Files/7-Zip/7z.exe`
5. `D:/Program Files (x86)/7-Zip/7z.exe`
6. System PATH (via `where` command)

**Example**:
```groovy
def sevenZip = find7ZipExecutable()
if (sevenZip) {
    println "Using 7-Zip: ${sevenZip}"
} else {
    println "7-Zip not found"
}
```

---

### `fetchModulesUntouchedProperties()`

Fetch `perl.properties` from the modules-untouched repository.

**Parameters**: None

**Returns**: Properties - Loaded properties, or null if fetch fails

**Description**:
- Downloads `perl.properties` from modules-untouched
- Caches to `bearsampp-build/tmp/downloads/perl/perl-untouched.properties`
- Returns Properties object with version-to-URL mappings

**URL**:
```
https://raw.githubusercontent.com/Bearsampp/modules-untouched/main/modules/perl.properties
```

**Example**:
```groovy
def props = fetchModulesUntouchedProperties()
if (props) {
    def url = props.getProperty("5.40.0")
    println "Perl 5.40.0 URL: ${url}"
}
```

---

### `downloadFromModulesUntouched(String version)`

Download Perl binaries from modules-untouched repository.

**Parameters**:
- `version` (String): Perl version to download (e.g., "5.40.0")

**Returns**: File - Downloaded archive file

**Description**:
- Fetches perl.properties to get download URL
- Falls back to standard URL pattern if not found
- Downloads to `bearsampp-build/tmp/downloads/perl/`
- Uses cached file if already downloaded

**URL Pattern** (fallback):
```
https://github.com/Bearsampp/modules-untouched/releases/download/perl-{version}/perl-{version}-win64.7z
```

**Example**:
```groovy
def archive = downloadFromModulesUntouched("5.40.0")
println "Downloaded: ${archive}"
```

**Throws**: RuntimeException if download fails

---

### `downloadAndExtractPerl(String version)`

Download and extract Perl binaries, returning the directory containing `perl.exe`.

**Parameters**:
- `version` (String): Perl version to download and extract

**Returns**: File - Directory containing `perl.exe`

**Description**:
- Downloads archive from modules-untouched
- Extracts to `bearsampp-build/tmp/extract/perl/{version}/`
- Searches for `perl.exe` in extracted files
- Returns directory containing `perl.exe`

**Example**:
```groovy
def perlDir = downloadAndExtractPerl("5.40.0")
println "Perl directory: ${perlDir}"
```

**Throws**: RuntimeException if download, extraction, or perl.exe search fails

---

### `generateHashFiles(File file)`

Generate hash sidecar files for an archive.

**Parameters**:
- `file` (File): Archive file to hash

**Returns**: void

**Description**:
- Generates MD5, SHA-1, SHA-256, and SHA-512 hashes
- Creates sidecar files with `.md5`, `.sha1`, `.sha256`, `.sha512` extensions
- Hash file format: `{hash} {filename}\n`

**Generated Files**:
- `{file}.md5`
- `{file}.sha1`
- `{file}.sha256`
- `{file}.sha512`

**Example**:
```groovy
def archive = file("bearsampp-perl-5.40.0-2025.4.26.7z")
generateHashFiles(archive)
// Creates:
//   bearsampp-perl-5.40.0-2025.4.26.7z.md5
//   bearsampp-perl-5.40.0-2025.4.26.7z.sha1
//   bearsampp-perl-5.40.0-2025.4.26.7z.sha256
//   bearsampp-perl-5.40.0-2025.4.26.7z.sha512
```

**Throws**: RuntimeException if file not found

---

### `getAvailableVersions()`

Get list of available Perl versions from `bin/` and `bin/archived/` directories.

**Parameters**: None

**Returns**: List<String> - List of version strings (e.g., ["5.40.0", "5.38.2"])

**Description**:
- Scans `bin/` directory for folders starting with `bundle.name`
- Scans `bin/archived/` directory for folders starting with `bundle.name`
- Extracts version from folder name (removes `bundle.name` prefix)
- Returns unique, sorted list

**Example**:
```groovy
def versions = getAvailableVersions()
versions.each { version ->
    println "Available: ${version}"
}
```

---

## Extension Points

### Custom Task Creation

You can create custom tasks that leverage the build system's helper functions.

**Example**:
```groovy
tasks.register('customBuild') {
    group = 'build'
    description = 'Custom build task'
    
    doLast {
        def versions = getAvailableVersions()
        versions.each { version ->
            println "Processing version: ${version}"
            // Custom logic here
        }
    }
}
```

---

### Custom Version Processing

Override version processing logic:

**Example**:
```groovy
tasks.register('customRelease') {
    group = 'build'
    description = 'Custom release with additional processing'
    
    doLast {
        def version = project.findProperty('bundleVersion')
        
        // Use helper functions
        def perlDir = findPerlDirectory(file("bin/perl${version}"))
        
        // Custom processing
        println "Custom processing for ${version}"
        
        // Call standard release logic
        tasks.getByName('release').actions.each { it.execute(tasks.getByName('release')) }
    }
}
```

---

## Properties API

### Project Properties

Access project properties in custom tasks:

**Example**:
```groovy
tasks.register('showProperties') {
    doLast {
        println "Bundle Name: ${bundleName}"
        println "Bundle Release: ${bundleRelease}"
        println "Bundle Type: ${bundleType}"
        println "Bundle Format: ${bundleFormat}"
        println "Build Base Path: ${buildBasePath}"
    }
}
```

---

### Available Properties

| Property              | Type   | Description                          |
|-----------------------|--------|--------------------------------------|
| `bundleName`          | String | Bundle name from build.properties    |
| `bundleRelease`       | String | Release version                      |
| `bundleType`          | String | Bundle type (tools, bins, apps)      |
| `bundleFormat`        | String | Archive format (7z, zip)             |
| `buildBasePath`       | String | Build root directory path            |
| `buildTmpPath`        | String | Temporary build files path           |
| `bundleTmpBuildPath`  | String | Bundle build staging path            |
| `bundleTmpPrepPath`   | String | Bundle preparation path              |
| `bundleTmpSrcPath`    | String | Bundle source path                   |
| `bundleTmpDownloadPath` | String | Download cache path                |
| `bundleTmpExtractPath` | String | Extract cache path                  |
| `gradleTmpBuildPath`  | String | Gradle build directory path          |

---

## Task API

### Task Registration

Register custom tasks using Gradle's task API:

**Example**:
```groovy
tasks.register('myTask') {
    group = 'custom'
    description = 'My custom task'
    
    doLast {
        println "Executing my task"
    }
}
```

---

### Task Dependencies

Add dependencies to existing tasks:

**Example**:
```groovy
tasks.named('release') {
    doFirst {
        println "Pre-release hook"
    }
    
    doLast {
        println "Post-release hook"
    }
}
```

---

### Task Configuration

Configure tasks at configuration time:

**Example**:
```groovy
tasks.register('configuredTask') {
    // Configuration time
    def myVersion = project.findProperty('bundleVersion') ?: '5.40.0'
    
    doLast {
        // Execution time
        println "Building version: ${myVersion}"
    }
}
```

---

## File Operations API

### Copy Operations

Use Gradle's copy API:

**Example**:
```groovy
tasks.register('copyFiles') {
    doLast {
        copy {
            from 'bin/perl5.40.0'
            into 'output/perl5.40.0'
            include '**/*.exe'
            include '**/*.dll'
        }
    }
}
```

---

### Delete Operations

Use Gradle's delete API:

**Example**:
```groovy
tasks.register('cleanCustom') {
    doLast {
        delete 'output'
        delete fileTree('tmp') {
            include '**/*.tmp'
        }
    }
}
```

---

## Exec API

### Execute External Commands

Use Gradle's exec API:

**Example**:
```groovy
tasks.register('runPerl') {
    doLast {
        exec {
            workingDir 'bin/perl5.40.0'
            commandLine 'perl.exe', '--version'
        }
    }
}
```

---

### Process Builder

Use Java's ProcessBuilder for more control:

**Example**:
```groovy
tasks.register('advancedExec') {
    doLast {
        def process = new ProcessBuilder('perl.exe', '--version')
            .directory(file('bin/perl5.40.0'))
            .redirectErrorStream(true)
            .start()
        
        process.inputStream.eachLine { line ->
            println line
        }
        
        def exitCode = process.waitFor()
        if (exitCode != 0) {
            throw new RuntimeException("Command failed with exit code: ${exitCode}")
        }
    }
}
```

---

## Logger API

### Logging Levels

Use Gradle's logger:

**Example**:
```groovy
tasks.register('loggingExample') {
    doLast {
        logger.error('Error message')
        logger.warn('Warning message')
        logger.lifecycle('Lifecycle message')
        logger.quiet('Quiet message')
        logger.info('Info message')
        logger.debug('Debug message')
    }
}
```

---

### Conditional Logging

Log based on conditions:

**Example**:
```groovy
tasks.register('conditionalLogging') {
    doLast {
        if (logger.isInfoEnabled()) {
            logger.info('Info logging is enabled')
        }
        
        if (logger.isDebugEnabled()) {
            logger.debug('Debug logging is enabled')
        }
    }
}
```

---

## Exception Handling

### Throwing Exceptions

Use RuntimeException for build failures:

**Example**:
```groovy
tasks.register('validateVersion') {
    doLast {
        def version = project.findProperty('bundleVersion')
        if (!version) {
            throw new RuntimeException('bundleVersion property is required')
        }
        
        def versionDir = file("bin/perl${version}")
        if (!versionDir.exists()) {
            throw new RuntimeException("Version directory not found: ${versionDir}")
        }
    }
}
```

---

### Try-Catch Blocks

Handle exceptions gracefully:

**Example**:
```groovy
tasks.register('safeDownload') {
    doLast {
        try {
            def archive = downloadFromModulesUntouched('5.40.0')
            println "Downloaded: ${archive}"
        } catch (Exception e) {
            logger.warn("Download failed: ${e.message}")
            logger.warn("Falling back to local files")
        }
    }
}
```

---

## API Examples

### Example 1: Custom Version Validator

```groovy
tasks.register('validateAllVersions') {
    group = 'verification'
    description = 'Validate all Perl versions'
    
    doLast {
        def versions = getAvailableVersions()
        def invalid = []
        
        versions.each { version ->
            def versionDir = file("bin/perl${version}")
            def perlDir = findPerlDirectory(versionDir)
            
            if (!perlDir) {
                invalid.add(version)
                logger.warn("Invalid version: ${version} (perl.exe not found)")
            } else {
                logger.lifecycle("Valid version: ${version}")
            }
        }
        
        if (!invalid.isEmpty()) {
            throw new RuntimeException("Invalid versions found: ${invalid.join(', ')}")
        }
        
        println "\n[SUCCESS] All ${versions.size()} versions are valid"
    }
}
```

---

### Example 2: Custom Archive Creator

```groovy
tasks.register('createCustomArchive') {
    group = 'build'
    description = 'Create custom archive with specific files'
    
    doLast {
        def version = project.findProperty('bundleVersion') ?: '5.40.0'
        def sourceDir = file("bin/perl${version}")
        def outputDir = file("custom-output")
        outputDir.mkdirs()
        
        // Copy specific files
        copy {
            from sourceDir
            into "${outputDir}/perl${version}"
            include '**/*.exe'
            include '**/*.dll'
            include 'lib/**'
        }
        
        // Create archive
        def archiveFile = file("${outputDir}/custom-perl-${version}.zip")
        ant.zip(destfile: archiveFile) {
            fileset(dir: outputDir) {
                include(name: "**/*")
            }
        }
        
        println "Created custom archive: ${archiveFile}"
    }
}
```

---

### Example 3: Version Comparison

```groovy
tasks.register('compareVersions') {
    group = 'help'
    description = 'Compare local and remote versions'
    
    doLast {
        def localVersions = getAvailableVersions()
        def remoteProps = fetchModulesUntouchedProperties()
        
        if (!remoteProps) {
            println "Could not fetch remote versions"
            return
        }
        
        def remoteVersions = remoteProps.keySet() as List
        
        println "\nLocal versions: ${localVersions.size()}"
        localVersions.each { println "  - ${it}" }
        
        println "\nRemote versions: ${remoteVersions.size()}"
        remoteVersions.each { println "  - ${it}" }
        
        def localOnly = localVersions - remoteVersions
        def remoteOnly = remoteVersions - localVersions
        
        if (!localOnly.isEmpty()) {
            println "\nLocal only:"
            localOnly.each { println "  - ${it}" }
        }
        
        if (!remoteOnly.isEmpty()) {
            println "\nRemote only:"
            remoteOnly.each { println "  - ${it}" }
        }
    }
}
```

---

**Last Updated**: 2025-04-26  
**Version**: 2025.4.26
