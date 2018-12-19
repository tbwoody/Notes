This is my first blog on ApacheFlink. It is a brief guide on how to set up IntelliJ IDEA IDE for the development of ApacheFlink. This blog is original from [Flink Development Importing Flink into an IDE](https://ci.apache.org/projects/flink/flink-docs-master/flinkDev/ide_setup.html#intellij-idea).

This blog is organized as follows:
- Download ApacheFlink Source Code
- Download and Install IntelliJ IDE
- Import ApacheFlink to IntelliJ IDE


**1. Download ApacheFlink Source Code**
The source code can be checked out from Apache's [repositories](https://flink.apache.org/community.html#source-code)
```
git clone https://github.com/apache/flink.git
cd flink
mvn clean package -DskipTests # this will take up to 10 minutes
```
Prerequisites for building Flink:

* Unix-like environment (we use Linux, Mac OS X, Cygwin)
* git
* Maven (we recommend version 3.2.5)
* Java 8 (Java 9 and 10 are not yet supported)

**2. Download and Install IntelliJ IDE**
The IntelliJ IDEA is downloaed from [DOWNLOAD PAGE.](https://www.jetbrains.com/idea/download/#section=linux)

Installation Instructions
- Unpack the idea idea-2018.3.2.tar.gz file to an empty directory using the following command: tar -xzf idea-2018.3.2.tar.gz
- Note: A new instance MUST NOT be extracted over an existing one. The target folder must be empty.
- Run idea.sh from the bin subdirectory.

**3. Import ApacheFlink to IntelliJ IDE**
1.Installing the Scala Plugin:
The IntelliJ installation setup offers to install the Scala plugin. If it is not installed, follow these instructions before importing Flink to enable support for Scala projects and files:

- Go to IntelliJ plugins settings (IntelliJ IDEA -> Preferences -> Plugins) and click on “Install Jetbrains plugin…”.
- Select and install the “Scala” plugin.
- Restart IntelliJ

2.Importing Flink:
- Start IntelliJ IDEA and choose “Import Project”
- Select the root folder of the Flink repository
- Choose “Import project from external model” and select “Maven”
- Leave the default options and click on “Next” until you hit the SDK section.
- If there is no SDK, create a one with the “+” sign top left, then click “JDK”, select your JDK home directory and click “OK”. Otherwise simply select your SDK.
- Continue by clicking “Next” again and finish the import.
- Right-click on the imported Flink project -> Maven -> Generate Sources and Update Folders. Note that this will install Flink libraries in your local Maven repository, i.e. “/home/-your-user-/.m2/repository/org/apache/flink/”. Alternatively, mvn clean package -DskipTests also creates the necessary files for the IDE to work with but without installing libraries.
- Build the Project (Build -> Make Project)

3.Checkstyle For Java:    
IntelliJ supports checkstyle within the IDE using the Checkstyle-IDEA plugin.    

- Install the “Checkstyle-IDEA” plugin from the IntelliJ plugin repository.
- Configure the plugin by going to Settings -> Other Settings -> Checkstyle.
- Set the “Scan Scope” to “Only Java sources (including tests)”.
- Select 8.9 in the “Checkstyle Version” dropdown and click apply. This step is important, don’t skip it!
- In the “Configuration File” pane, add a new configuration using the plus icon:
  - Set the “Description” to “Flink”.
  - Select “Use a local Checkstyle file”, and point it to "tools/maven/checkstyle.xml" within your repository.
  - Check the box for “Store relative to project location”, and click “Next”.
  - Configure the “checkstyle.suppressions.file” property value to "suppressions.xml", and click “Next”, then “Finish”.
- Select “Flink” as the only active configuration file, and click “Apply” and “OK”.
- Checkstyle will now give warnings in the editor for any Checkstyle violations.
Once the plugin is installed you can directly import "tools/maven/checkstyle.xml" by going to Settings -> Editor -> Code Style -> Java -> Gear Icon next to Scheme dropbox. This will for example automatically adjust the imports layout.

You can scan an entire module by opening the Checkstyle tools window and clicking the “Check Module” button. The scan should report no errors.

*Note Some modules are not fully covered by checkstyle, which include flink-core, flink-optimizer, and flink-runtime. Nevertheless please make sure that code you add/modify in these modules still conforms to the checkstyle rules.

4.Checkstyle For Scala:    
Enable scalastyle in Intellij by selecting Settings -> Editor -> Inspections, then searching for “Scala style inspections”. Also Place "tools/maven/scalastyle_config.xml" in the "<root>/.idea" or "<root>/project" directory.
