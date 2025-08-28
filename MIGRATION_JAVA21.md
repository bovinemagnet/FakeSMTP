# Migration to Java 21

This document describes the changes made to upgrade FakeSMTP from Java 1.6 to Java 21.

## Overview

FakeSMTP has been modernized to use Java 21, bringing improved performance, better security, and access to modern Java features. This upgrade ensures compatibility with current development environments and dependency ecosystems.

## Major Changes

### 1. Java Version Upgrade
- **Previous**: Java 1.6
- **Current**: Java 21
- **Impact**: Requires Java 21 or higher to build and run

### 2. Build Configuration

#### Maven Compiler Plugin
```xml
<!-- Old -->
<maven-compiler-plugin.version>3.3</maven-compiler-plugin.version>
<source>1.6</source>
<target>1.6</target>

<!-- New -->
<maven-compiler-plugin.version>3.11.0</maven-compiler-plugin.version>
<source>21</source>
<target>21</target>
<release>21</release>
```

### 3. Dependency Updates

| Dependency | Old Version | New Version | Notes |
|------------|-------------|-------------|-------|
| MigLayout | 5.0 | 11.3 | Swing layout manager |
| SLF4J API | 1.7.12 | 2.0.9 | Breaking changes in 2.x |
| Logback | 1.1.3 | 1.4.14 | Compatible with SLF4J 2.x |
| Commons IO | 2.4 | 2.15.1 | Security and performance improvements |
| Commons CLI | 1.2 | 1.6.0 | Bug fixes and improvements |
| JUnit | 4.12 | 4.13.2 | Security patches |
| Commons Email | 1.3.3 | 1.5 | Updated for newer Java |

### 4. Maven Plugin Updates

| Plugin | Old Version | New Version |
|--------|-------------|-------------|
| maven-compiler-plugin | 3.3 | 3.11.0 |
| maven-failsafe-plugin | 2.18.1 | 3.2.3 |
| maven-assembly-plugin | 2.5.4 | 3.6.0 |
| maven-site-plugin | 3.4 | 4.0.0-M13 |
| maven-surefire-report-plugin | 2.18.1 | 3.2.3 |
| maven-javadoc-plugin | 2.10.3 | 3.6.3 |
| maven-project-info-reports-plugin | 2.8 | 3.5.0 |
| maven-pmd-plugin | 3.4 | 3.21.2 |
| maven-checkstyle-plugin | 2.15 | 3.3.1 |

### 5. Code Changes

#### Apple Java Extensions Removal
The deprecated Apple Java Extensions library has been removed and replaced with standard Java APIs:

**Old Code (using com.apple.eawt.Application):**
```java
import com.apple.eawt.Application;

// Setting dock icon (macOS)
Application.getApplication().setDockIconImage(image);
Application.getApplication().setDockIconBadge(badgeValue);
```

**New Code (using java.awt.Taskbar):**
```java
import java.awt.Taskbar;

// Cross-platform taskbar support
if (Taskbar.isTaskbarSupported()) {
    if (Taskbar.getTaskbar().isSupported(Taskbar.Feature.ICON_IMAGE)) {
        Taskbar.getTaskbar().setIconImage(image);
    }
    if (Taskbar.getTaskbar().isSupported(Taskbar.Feature.ICON_BADGE_TEXT)) {
        Taskbar.getTaskbar().setIconBadge(badgeValue);
    }
}
```

Benefits:
- Cross-platform compatibility (Windows, macOS, Linux)
- No external dependencies required
- Standard Java API (available since Java 9)

### 6. Known Issues

#### Deprecated APIs Still in Use
The following deprecated APIs are still present but functional:
- `java.util.Observable` and `java.util.Observer` (deprecated since Java 9)
  - Used throughout the GUI for event handling
  - Still functional but should be migrated to `PropertyChangeSupport` or other event mechanisms in future updates

#### Build Warnings
You may see warnings about deprecated APIs during compilation. These are non-critical and the application functions correctly.

## Migration Guide for Developers

### For Users Running FakeSMTP

1. **Install Java 21 or higher**
   - Download from: https://adoptium.net/ or https://www.oracle.com/java/technologies/downloads/
   - Verify installation: `java -version`

2. **Run the Application**
   ```bash
   java -jar fakeSMTP-VERSION.jar
   ```

### For Developers Building from Source

1. **Install Prerequisites**
   - Java JDK 21 or higher
   - Maven 3.6+ (optional, Maven Wrapper included)

2. **Build the Project**
   ```bash
   # Using Maven Wrapper (recommended)
   ./mvnw clean package
   
   # Or using Maven directly
   mvn clean package
   ```

3. **Run Tests**
   ```bash
   # Unit tests
   ./mvnw test
   
   # Integration tests (requires server on port 2525)
   java -jar target/fakeSMTP-VERSION.jar -p 2525 -s
   ./mvnw integration-test
   ```

## Benefits of Java 21

1. **Performance Improvements**
   - Virtual threads for better concurrency
   - Improved garbage collection
   - Better JIT compilation

2. **Security Updates**
   - Latest security patches
   - Modern TLS support
   - Updated cryptographic algorithms

3. **Developer Experience**
   - Pattern matching
   - Records
   - Text blocks
   - Switch expressions
   - Helpful NullPointerExceptions

4. **Long-term Support**
   - Java 21 is an LTS (Long Term Support) release
   - Supported until at least 2029

## Rollback Instructions

If you need to use the Java 1.6 version:
1. Checkout the commit before the Java 21 upgrade
2. Ensure Java 1.6+ is installed
3. Build with the old configuration

## Future Improvements

Potential areas for future modernization:
1. Replace Observable/Observer pattern with PropertyChangeSupport or reactive streams
2. Migrate to JavaFX or modern Swing features
3. Add module support (Java Platform Module System)
4. Implement virtual threads for email processing
5. Add native image support with GraalVM

## Support

For issues related to the Java 21 migration:
1. Check Java version compatibility: `java -version`
2. Clear Maven cache if build issues occur: `rm -rf ~/.m2/repository`
3. Report issues at: https://github.com/Nilhcem/FakeSMTP/issues