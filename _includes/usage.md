# Usage

`examples/maven` is a simple Maven project which uses TestPackage. The following elements are core prerequisites for using TestPackage:

### TestPackage library dependency

{% highlight xml %}
<dependency>
    <groupId>org.testpackage</group>
    <artifactId>testpackage</artifactId>
</dependency>
{% endhighlight %}

### Maven plugin to compile test sources, dependencies and TestPackage into a single executable JAR

*Note that `package-name` must be set to the package where test classes reside. `flags` may be set to configure JVM properties for the fat jar if invoked as a plain unix executable.*

{% highlight xml %}
<plugin>
    <groupId>org.testpackage</groupId>
    <artifactId>testpackage-maven-plugin</artifactId>
    <executions>
        <execution>
            <phase>package</phase>
            <goals>
                <goal>fatjar</goal>
            </goals>
            <configuration>
                <package-name>org.testpackage.example.maven</package-name>
                <flags>-Xmx2G</flags>
            </configuration>
        </execution>
    </executions>
</plugin>
{% endhighlight %}

### Configuration of integration test source location

*N.B. This is not needed if you don't mind keeping integration test sources under src/main/java)*

{% highlight xml %}
<plugin>
    <groupId>org.codehaus.mojo</groupId>
    <artifactId>build-helper-maven-plugin</artifactId>
    <version>1.4</version>
    <executions>
        <execution>
            <id>add-test-source</id>
            <phase>generate-sources</phase>
            <goals>
                <goal>add-source</goal>
            </goals>
            <configuration>
                <sources>
                    <source>${basedir}/src/int-test/java</source>
                </sources>
            </configuration>
        </execution>
    </executions>
</plugin>
{% endhighlight %}

## Building standalone test JAR with Maven

With the configuration outlined above, simply run `mvn clean package` to produce an executable JAR.

## Running the tests

Once built, execute the build JAR file (e.g. named `maven-example-1.0-SNAPSHOT.jar`) by running:

    java -jar target/maven-example-1.0-SNAPSHOT.jar

Of course, this JAR file can be moved anywhere on the filesystem, deployed to an artifact repository, etc (e.g. with `mvn deploy`).

After tests have run, a directory named `.testpackage` will be created in the working directory. This directory contains
some metadata used internally by TestPackage to prioritise recently-failed tests over tests that have never failed or not failed recently. _To get the most value out of the test prioritisation support, you should not remove this folder_. In fact, you should check this folder into version control or configure your CI system to retain it between test runs. Doing so will ensure that CI always runs recently-failed tests first.

While the above runs with sensible defaults, the following arguments may be passed at the command line to customise behaviour:

### Options

    --failfast or -ff:                         Enables fail-fast mode
    --quiet or -q:                             Enables quiet mode (hides all test output)
    --verbose or -v:                           Enables verbose mode (shows all test output, including stdout/stderr)
    --shard=n:                                 The index of the current test shard (default: 0)
    --numShards=n:                             If sharding, set this to the number of shards (default: 1)
    --propertiesfile=name or -P name:          Load a properties file (either plain Java .properties or XML) and set system properties accordingly during test run.

### Arguments

The full package names which should be searched (non-recursively) for test classes

### Usage

    java -jar JARFILE [OPTIONS] [ARGUMENTS]
