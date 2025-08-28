# FakeSMTP Quick Start Guide

## Prerequisites
- Java 21 or higher ([Download](https://adoptium.net/))
- Git (for building from source)

## Running FakeSMTP

### Option 1: Download Pre-built JAR
1. Download the latest release from [GitHub Releases](https://github.com/Nilhcem/FakeSMTP/releases)
2. Run: `java -jar fakeSMTP-VERSION.jar`

### Option 2: Build from Source
```bash
# Clone the repository
git clone https://github.com/Nilhcem/FakeSMTP.git
cd FakeSMTP

# Build with Maven Wrapper
./mvnw clean package -DskipTests

# Run the application
java -jar target/fakeSMTP-2.1-SNAPSHOT.jar
```

## Common Usage Examples

### Basic GUI Mode
```bash
java -jar fakeSMTP.jar
```
1. Click "Start server" button
2. Choose port (default 25 requires root/admin on Unix-like systems)
3. Select output directory for emails (optional)
4. Send test emails to localhost:port

### Headless/Background Mode
```bash
# Start on port 2525, no GUI, save emails to ./emails directory
java -jar fakeSMTP.jar -s -b -p 2525 -o ./emails

# Start on port 2525, no GUI, memory-only (no file saving)
java -jar fakeSMTP.jar -s -b -p 2525 -m
```

### Command-Line Options
| Option | Long Form | Description |
|--------|-----------|-------------|
| `-p` | `--port` | SMTP port number (default: 25) |
| `-b` | `--background` | Run in background without GUI |
| `-s` | `--start-server` | Start SMTP server automatically |
| `-o` | `--output-dir` | Directory to save emails |
| `-m` | `--memory-mode` | Don't save emails to disk |
| `-a` | `--bind-address` | IP address to bind (default: all) |
| `-r` | `--relay-domains` | Comma-separated list of allowed domains |
| `-h` | `--help` | Show help message |

## Testing Your Setup

### Using telnet
```bash
telnet localhost 2525
HELO test
MAIL FROM: <sender@example.com>
RCPT TO: <recipient@example.com>
DATA
Subject: Test Email
This is a test message.
.
QUIT
```

### Using Python
```python
import smtplib
from email.mime.text import MIMEText

msg = MIMEText('This is a test email')
msg['Subject'] = 'Test Subject'
msg['From'] = 'sender@example.com'
msg['To'] = 'recipient@example.com'

s = smtplib.SMTP('localhost', 2525)
s.send_message(msg)
s.quit()
```

### Using Java
```java
import org.apache.commons.mail.SimpleEmail;

SimpleEmail email = new SimpleEmail();
email.setHostName("localhost");
email.setSmtpPort(2525);
email.setFrom("sender@example.com");
email.addTo("recipient@example.com");
email.setSubject("Test Subject");
email.setMsg("This is a test email");
email.send();
```

### Using Node.js
```javascript
const nodemailer = require('nodemailer');

const transporter = nodemailer.createTransport({
  host: 'localhost',
  port: 2525,
  secure: false
});

transporter.sendMail({
  from: 'sender@example.com',
  to: 'recipient@example.com',
  subject: 'Test Subject',
  text: 'This is a test email'
});
```

## Troubleshooting

### Port 25 Permission Denied
On Unix-like systems (Linux, macOS), use a port >= 1024 or run with sudo:
```bash
# Use higher port
java -jar fakeSMTP.jar -p 2525

# Or with sudo (not recommended)
sudo java -jar fakeSMTP.jar -p 25
```

### Java Version Error
```bash
# Check Java version
java -version

# Should show: java version "21" or higher
# If not, download Java 21 from https://adoptium.net/
```

### Can't Connect to Server
1. Check if server is running (look for "Server started" message)
2. Check firewall settings
3. Try connecting to localhost instead of 127.0.0.1 or vice versa
4. Ensure no other application is using the same port

### Emails Not Saving
1. Check write permissions for output directory
2. Ensure sufficient disk space
3. Try without `-m` (memory mode) flag
4. Check application logs in the "Logs" tab

## Docker Usage

### Using Pre-built Image
```bash
docker pull nilhcem/fakesmtp
docker run -d -p 2525:25 -v /tmp/emails:/output nilhcem/fakesmtp
```

### Building Custom Image
```bash
# Build from source
git clone https://github.com/Nilhcem/FakeSMTP.git
cd FakeSMTP
./mvnw package docker:build -DskipTests

# Run container
docker run -d -p 2525:25 -v /tmp/emails:/output fakesmtp
```

## Integration with CI/CD

### GitHub Actions
```yaml
- name: Start FakeSMTP
  run: |
    wget https://github.com/Nilhcem/FakeSMTP/releases/download/VERSION/fakeSMTP.jar
    java -jar fakeSMTP.jar -s -b -p 2525 &
    sleep 5  # Wait for server to start

- name: Run tests
  run: npm test  # Your test command
```

### Jenkins Pipeline
```groovy
stage('Start FakeSMTP') {
  steps {
    sh 'java -jar fakeSMTP.jar -s -b -p 2525 &'
    sh 'sleep 5'
  }
}
```

### GitLab CI
```yaml
before_script:
  - java -jar fakeSMTP.jar -s -b -p 2525 &
  - sleep 5
```

## Advanced Configuration

### Custom Relay Domains
Allow emails only from specific domains:
```bash
java -jar fakeSMTP.jar -r "example.com,test.com"
```

### Binding to Specific Interface
```bash
# Bind to localhost only
java -jar fakeSMTP.jar -a 127.0.0.1

# Bind to specific network interface
java -jar fakeSMTP.jar -a 192.168.1.100
```

### Memory Optimization
For high-volume testing without disk I/O:
```bash
java -Xmx512m -jar fakeSMTP.jar -m -s -b -p 2525
```

## Contributing

See [README.md](README.md) for build instructions and contribution guidelines.

## Support

- [GitHub Issues](https://github.com/Nilhcem/FakeSMTP/issues)
- [Documentation](README.md)
- [Migration Guide](MIGRATION_JAVA21.md)