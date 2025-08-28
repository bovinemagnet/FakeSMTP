FakeSMTP
========

FakeSMTP is a Free Fake SMTP Server with GUI for testing emails in applications easily.
It is written in Java.

<img src="http://nilhcem.github.io/FakeSMTP/images/screenshot_mac.png" width="664" height="463" />

Configure your application to use `localhost` as your SMTP server, and all
emails will be intercepted and displayed in this software.

FakeSMTP uses SubEthaSMTP: an easy-to-use server-side SMTP library for Java.

FakeSMTP is free to use for commercial and non-commercial projects and the
source code is provided.

It is licensed under the very free BSD or GPL license, whichever you prefer.


## Latest Updates

- **Java 21 Support**: The application has been updated to use Java 21 with modernized dependencies
- **Cross-platform Compatibility**: Replaced deprecated Apple Java Extensions with standard Java APIs  
- **Updated Dependencies**: All major dependencies have been updated to their latest stable versions
- **Documentation**: 
  - [Quick Start Guide](QUICK_START.md) - Get up and running quickly
  - [Migration Guide](MIGRATION_JAVA21.md) - Detailed Java 21 upgrade information


Requirements
------------

You need Java JVM 21 or newer installed on your machine.

If you are on a "Unix-like" machine (Mac, GNU/Linux, BSD...), you may have
to be "root" to start the port `25`, otherwise, try another port >= `1024`.


Usage
-----

The fakeSMTP.jar is auto-executable.
If your desktop environment supports it, you can directly double click
on the .jar file.
Otherwise, run the following command:

    java -jar fakeSMTP-VERSION.jar

If you want to specify the directory where emails will be saved when starting the application, you can use the `-o` argument:

    java -jar fakeSMTP-VERSION.jar -o output_directory_name
    java -jar fakeSMTP-VERSION.jar --output-dir output_directory_name

If you want to autostart the SMTP server at launch, you can use the `-s` argument:

    java -jar fakeSMTP-VERSION.jar -s
    java -jar fakeSMTP-VERSION.jar --start-server

If you want to autostart the SMTP server without a GUI (background) on a different port and bound to the loopback address:

    java -jar fakeSMTP-VERSION.jar -s -b -p 2525 -a 127.0.0.1
    java -jar fakeSMTP-VERSION.jar --start-server --background --port 2525 --bind-address 127.0.0.1

If you don't need to save emails on the filesystem (to improve the overall performances), you can use the `-m` (memory mode) argument:

    java -jar fakeSMTP-VERSION.jar -m

To see all the available options (relay domains, custom eml-viewer...):

    java -jar fakeSMTP-VERSION.jar --help


Alternatives
------------

FakeSMTP was created because we couldn't find any free (as in freedom) and
cross-platform SMTP server with GUI for testing emails in applications or websites.
Listed below are some greats alternatives to Fake SMTP:


**[SMTP4dev](http://smtp4dev.codeplex.com/)**

* Nice features;
* Open source;
* Windows only (written in .Net).


**[DevNull SMTP](http://www.aboutmyip.com/AboutMyXApp/DevNullSmtp.jsp)**

* Lightweight;
* Closed source;
* Cross-Platform (written in Java 1.4).


Building from Source
--------------------

### Prerequisites
- Java JDK 21 or higher
- Maven 3.6+ (or use the included Maven Wrapper)

### Build Commands

Using the Maven Wrapper (recommended):

    ./mvnw clean package              # Build with tests
    ./mvnw clean package -DskipTests  # Build without tests

Or with Maven directly:

    mvn clean package                  # Build with tests
    mvn clean package -DskipTests      # Build without tests

The executable JAR will be created in the `target/` folder as `fakeSMTP-VERSION.jar`.


Running integration tests
-------------------------

To run integration tests, you will first need to launch the application
and start the server on port `2525`.

    java -jar fakeSMTP-VERSION.jar -p 2525 -s

You can then run the following command:

    mvn integration-test


Testing Configuration
--------------------

### Change the default port for unit/integration tests

Modify the test configuration in:
`src/test/java/com/nilhcem/fakesmtp/core/test/TestConfig.java`

- Unit tests default port: 2626
- Integration tests default port: 2525

Using different ports for unit and integration tests prevents port binding conflicts when running Maven's `integration-test` goal.


## Technical Details

### Minimum Requirements
- Java 21 or higher
- 128MB RAM
- Any operating system with Java support (Windows, macOS, Linux, BSD)

### Key Dependencies
- SubEthaSMTP 3.1.7 - SMTP server implementation
- MigLayout 11.3 - Swing layout manager
- SLF4J 2.0.9 + Logback 1.4.14 - Logging framework
- Commons IO 2.15.1 - File operations
- Commons CLI 1.6.0 - Command-line parsing


Usage on Docker
---------------

* Run distributed version: [Dockerfile](https://github.com/Nilhcem/FakeSMTP/blob/master/Dockerfile)

      `docker build -t="mail" github.com/Nilhcem/FakeSMTP`

      `docker run -ti -p 250:25 --privileged=true -v /mail:/output mail`


* Build from source

Get sources from GitHub: [Dockerfile](https://github.com/Nilhcem/FakeSMTP/blob/master/src/main/docker/Dockerfile)

    git clone https://github.com/Nilhcem/FakeSMTP
    cd FakeSMTP

Build the docker image

    mvn package docker:build -DskipTests

Run the docker image

    docker run -ti -d fakesmtp

Configure container

* Map the SMTP port 25 to host:

    `-p 250:25`

* Map volume for received mails:

    `--privileged=true  -v /mail-data:/output`

Full command

* Foward fakesmtp:25 to host port 250,
* mount host folder /home/fakesmtp/mail as container folder /output

    `docker run -ti -d -p 250:25 --privileged=true -v /home/fakesmtp/mail:/output fakesmtp`


Contact me
----------

Use my github's nickname (at) gmail (dot) com
