# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

FakeSMTP is a Java-based fake SMTP server with GUI for testing email functionality in applications. It intercepts emails sent to localhost and displays them in the GUI without actually sending them.

**Requirements**: 
- Java 21 or higher
- Maven 3.6+ (or use included Maven Wrapper)

**Recent Updates**:
- Migrated from Java 1.6 to Java 21
- Updated all dependencies to latest stable versions
- Replaced deprecated Apple Java Extensions with cross-platform Java APIs

## Build and Development Commands

### Building the Project
```bash
# Build with Maven wrapper (recommended)
./mvnw clean package              # With tests
./mvnw clean package -DskipTests  # Without tests

# Build with specific Java version
JAVA_HOME=/path/to/java21 ./mvnw clean package

# Build Docker image
./mvnw package docker:build -DskipTests
```

### Common Build Issues

1. **Java Version**: Ensure Java 21+ is installed (`java -version`)
2. **Maven Cache**: Clear if issues occur (`rm -rf ~/.m2/repository`)
3. **Deprecated APIs**: Warnings about Observable/Observer are expected and non-critical

### Running the Application
```bash
# Run the JAR with GUI
java -jar target/fakeSMTP-2.1-SNAPSHOT.jar

# Run with specific output directory
java -jar target/fakeSMTP-2.1-SNAPSHOT.jar -o output_directory

# Run in background mode on specific port
java -jar target/fakeSMTP-2.1-SNAPSHOT.jar -s -b -p 2525 -a 127.0.0.1

# Run in memory mode (no file saving)
java -jar target/fakeSMTP-2.1-SNAPSHOT.jar -m
```

### Testing
```bash
# Run unit tests
./mvnw test

# Run integration tests (requires server running on port 2525)
# First start the server:
java -jar target/fakeSMTP-2.1-SNAPSHOT.jar -p 2525 -s
# Then run integration tests:
./mvnw integration-test
```

### Code Quality and Reports
```bash
# Generate site with reports (Findbugs, PMD, Checkstyle, Cobertura, etc.)
./mvnw site
```

## Architecture Overview

### Core Components

1. **Entry Point**: `FakeSMTP.java` - Main class that handles application startup, command-line arguments processing, and GUI initialization.

2. **Server Layer** (`com.nilhcem.fakesmtp.server`):
   - `SMTPServerHandler` - Singleton that manages the SubEthaSMTP server lifecycle
   - `MailListener` - Implements SimpleMessageListener to receive emails
   - `MailSaver` - Handles saving emails to filesystem as .eml files
   - `SMTPAuthHandler/Factory` - Handles SMTP authentication

3. **GUI Layer** (`com.nilhcem.fakesmtp.gui`):
   - `MainFrame` - Main application window using Swing
   - `MainPanel` - Contains server controls and tabbed panes for emails
   - Tab components: `MailsListPane`, `LastMailPane`, `LogsPane`
   - Info components: Port field, start/stop button, save directory chooser

4. **Core Utilities** (`com.nilhcem.fakesmtp.core`):
   - `ArgsHandler` - Command-line argument parsing using Commons CLI
   - `Configuration` - Loads application properties
   - `I18n` - Internationalization support (supports 8 languages)
   - Exception classes for port binding and validation

5. **Model Layer** (`com.nilhcem.fakesmtp.model`):
   - `EmailModel` - Represents an email message
   - `UIModel` - Singleton managing UI state and email collection

6. **Logging** (`com.nilhcem.fakesmtp.log`):
   - Custom Logback appender to display logs in GUI
   - Observable pattern for log updates

### Key Design Patterns

- **Singleton Pattern**: Used for `SMTPServerHandler`, `UIModel` for global state management
- **Observer Pattern**: Used for log updates and email notifications to GUI
- **MVC Pattern**: Separation between models, GUI views, and server controllers

### Important Configuration

- Default ports for testing configured in `TestConfig.java`:
  - Unit tests: port 2626
  - Integration tests: port 2525
- Internationalization files in `src/main/resources/i18n/`
- Checkstyle rules in `src/main/config/checkstyle/custom_checks.xml`
- Application icon and splash screen in resources

### Dependencies (Updated for Java 21)

- **SubEthaSMTP 3.1.7**: Core SMTP server functionality
- **MigLayout 11.3**: Modern Swing layout manager
- **SLF4J 2.0.9 + Logback 1.4.14**: Logging framework (major version upgrade)
- **Commons IO 2.15.1**: File operations with security updates
- **Commons CLI 1.6.0**: Command-line parsing
- **JUnit 4.13.2**: Testing framework with security patches
- **Apache Commons Email 1.5**: For integration testing

### Known Technical Debt

- **Deprecated Observer Pattern**: The codebase still uses `java.util.Observable` and `java.util.Observer` (deprecated since Java 9). These work but should be migrated to `PropertyChangeSupport` or similar patterns.
- **Legacy Swing UI**: Could benefit from modern Swing features or migration to JavaFX