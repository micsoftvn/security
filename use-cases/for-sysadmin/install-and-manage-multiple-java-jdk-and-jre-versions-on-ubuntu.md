# INSTALL AND MANAGE MULTIPLE JAVA JDK AND JRE VERSIONS ON UBUNTU

**INSTALL AND MANAGE MULTIPLE JAVA JDK AND JRE VERSIONS ON UBUNTU**

**Step 1. Check Java JDK version**

Open a terminal and check java JDK version:

```plaintext
$ javac -version
```

If you do not have a Java JDK installed the terminal response will look like:

```plaintext
Command 'javac' not found, but can be installed with:

sudo apt install default-jdk              # version 2:1.11-72, or
sudo apt install openjdk-11-jdk-headless  # version 11.0.8+10-0ubuntu1~20.04
sudo apt install openjdk-13-jdk-headless  # version 13.0.4+8-1~20.04
sudo apt install openjdk-14-jdk-headless  # version 14.0.2+12-1~20.04
sudo apt install openjdk-8-jdk-headless   # version 8u265-b01-0ubuntu2~20.04
sudo apt install ecj                      # version 3.16.0-1
```

As you can see, the terminal response displays the commands to install various headless JDK versions.

**Step 2. Install Java JRE/JDKs**

**1. Install Java 11 JRE/JDK**

At the time of writing, Java 11 is the latest long-term supported (LTS) version of Java. It is the default Java development and runtime version.

Installing a JDK package will also install the corresponding JRE.

Install Java 11 JDK:

```plaintext
$ sudo apt install default-jdk
```

Check JRE version:

```plaintext
$ java -version
```

The response will look like:

```plaintext
openjdk version "11.0.9" 2020-10-20
OpenJDK Runtime Environment (build 11.0.9+11-Ubuntu-0ubuntu1.20.04)
OpenJDK 64-Bit Server VM (build 11.0.9+11-Ubuntu-0ubuntu1.20.04, mixed mode, sharing)
```

Check JDK version:

```plaintext
$ javac -version
```

The response will look like:

```plaintext
javac 11.0.9
```

**2. Install Java 8 JRE/JDK**

Install Java 8 JDK:

```plaintext
$ sudo apt install openjdk-8-jdk
```

If you check current JDK and JRE versions it will still display Java 11.

**3. Install Java 13 JRE/JDK**

Install Java 13 JDK:

```plaintext
$ sudo apt install openjdk-13-jdk
```

Check current JRE version:

```plaintext
$ java -version
```

The response will look like:

```plaintext
openjdk version "13.0.4" 2020-07-14
OpenJDK Runtime Environment (build 13.0.4+8-Ubuntu-120.04)
OpenJDK 64-Bit Server VM (build 13.0.4+8-Ubuntu-120.04, mixed mode)
```

Check current JDK version:

```plaintext
$ javac -version
```

The response will look like:

```plaintext
javac 13.0.4
```

As you can see the current JRE and JDK are version 13.

**4. Install Java 14 JRE/JDK**

Install Java 14 JDK:

```plaintext
$ sudo apt install openjdk-14-jdk
```

Check current JRE:

```plaintext
$ java -version
```

The response will look like:

```plaintext
openjdk version "14.0.2" 2020-07-14
OpenJDK Runtime Environment (build 14.0.2+12-Ubuntu-120.04)
OpenJDK 64-Bit Server VM (build 14.0.2+12-Ubuntu-120.04, mixed mode, sharing)
```

Check current JDK:

```plaintext
$ javac -version
```

The response will look like:

```plaintext
javac 14.0.2
```

Again, we can see the current JRE and JDK are version 14.

**Step 2. Manage Installed Java JRE/JDKs**

Now that we have installed multiple Java JRE/JDK versions we can switch between them.

**1. Switch JRE version**

Check installed JREs:

```plaintext
$ sudo update-alternatives --config java
```

The response will look like:

```plaintext
There are 4 choices for the alternative java (providing /usr/bin/java).

  Selection    Path                                            Priority   Status
------------------------------------------------------------
* 0            /usr/lib/jvm/java-14-openjdk-amd64/bin/java      1411      auto mode
  1            /usr/lib/jvm/java-11-openjdk-amd64/bin/java      1111      manual mode
  2            /usr/lib/jvm/java-13-openjdk-amd64/bin/java      1311      manual mode
  3            /usr/lib/jvm/java-14-openjdk-amd64/bin/java      1411      manual mode
  4            /usr/lib/jvm/java-8-openjdk-amd64/jre/bin/java   1081      manual mode

Press <enter> to keep the current choice[*], or type selection number: 
```

Now you can switch to another JRE or keep the current version.

**2. Switch JDK version**

Check installed JDKs:

```plaintext
$ sudo update-alternatives --config javac 
```

The response will look like:

```plaintext
There are 4 choices for the alternative javac (providing /usr/bin/javac).

  Selection    Path                                          Priority   Status
------------------------------------------------------------
* 0            /usr/lib/jvm/java-14-openjdk-amd64/bin/javac   1411      auto mode
  1            /usr/lib/jvm/java-11-openjdk-amd64/bin/javac   1111      manual mode
  2            /usr/lib/jvm/java-13-openjdk-amd64/bin/javac   1311      manual mode
  3            /usr/lib/jvm/java-14-openjdk-amd64/bin/javac   1411      manual mode
  4            /usr/lib/jvm/java-8-openjdk-amd64/bin/javac    1081      manual mode

Press <enter> to keep the current choice[*], or type selection number: 
```

Again, you can now switch to another JDK or keep the current version.

**Step 3. Add JAVA\_HOME environment variable**

Java applications may use environment variables. JAVA\_HOME is a common one so we will now we add this.

Edit /etc/environment file:

```plaintext
$ sudo nano /etc/environment
```

Add the following line to the file and save:

```plaintext
JAVA_HOME="/usr/lib/jvm/java-11-openjdk-amd64/bin/"
```

In the above example we are using Java 11.

To apply the changes you will need to log out then log in again. To apply the changes in your current terminal session use the source command:

```plaintext
$ source /etc/environment
```

Check the environment variable was set:

```plaintext
$ echo $JAVA_HOME
```

The response should look like:

```plaintext
sr/lib/jvm/java-11-openjdk-amd64/bin/
```
