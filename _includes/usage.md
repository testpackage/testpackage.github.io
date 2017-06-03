# Usage

`examples/maven` is a simple Maven project which uses TestPackage. The following elements are core prerequisites for using TestPackage:

### TestPackage library dependency

{% highlight xml %}
<dependency>
    <groupId>org.testpackage</groupId>
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

## Usage

If you have built a fat jar file which includes TestPackage as a dependency, it can be run as follows:

    java -jar JARFILE [OPTIONS] [ARGUMENTS]

If you have used the TestPackage maven plugin to build your test fat jar, it will be directly executable as well as being executable via `java -jar`:

    java -jar JARFILE [OPTIONS] [ARGUMENTS]
    or
    JARFILE [OPTIONS] [ARGUMENTS]

### Options

#### Fail fast

`--failfast` or `-ff`: Abort testing when the first failure occurs (good for validation before pushing changes to the team repository)

#### Quiet mode

`--quiet` or `-q`: Enables quiet mode (hides all test output)

#### Verbose mode

`--verbose` or `-v`: Enables verbose mode (shows all test output, including stdout/stderr)

#### Sharding

`--shard=n`: Set the index of the current test shard (default: 0)

`--numShards=n`: Set this to the total number of shards (default: 1)

#### Loading environment variables from a file

`--propertiesfile=name` or `-P name`: Load a properties file (either plain Java .properties or XML) and set system properties accordingly during test run.

#### Coverage optimization

##### Recording coverage data

N.B. To record coverage data:

 * `org.testpackage:coverage-jacoco` must be on the test package's classpath
 * The JacoCo coverage agent must be injected into the system under test in TCP server mode (e.g. adding a Java agent option like `-javaagent:path/to/jacocoagent.jar=output=tcpserver`)

See `examples/coverage` for an example.

`--jacoco-host`: Specify which host is running the system under test (default: localhost)

`--jacoco-port`: Specify which port to find the JaCoCo agent on (if running. default: 6300)

##### Optimizing tests once coverage data has been collected

`--optimize-coverage=n%`: Tell TestPackage to aim for n% test coverage. If attainable, it will try to select the quickest subset of tests that achieve this coverage.

`--optimize-time=x`: Tell TestPackage to aim for x test execution time (e.g. 1m, 1min, 1h). It will try and select a subset of tests which achieve the best coverage without exceeding this execution time.

### Arguments

The full package names which should be searched (non-recursively) for test classes

### Usage examples

Run all test classes found in `org.example.tests`:

    target/functests.jar org.example.tests


Run tests spread across three different test executors (note: afterwards, JUnit test report files output to `target` need to be merged separately):

    target/functests.jar --shard=1 --numShards=3 org.example.tests          # On executor 1
    target/functests.jar --shard=2 --numShards=3 org.example.tests          # On executor 2
    target/functests.jar --shard=3 --numShards=3 org.example.tests          # On executor 3


Record test coverage (e.g. with the app being tested running in Tomcat):

    # Start Tomcat with a JaCoCo coverage recording agent
    export CATALINA_OPTS="$CATALINA_OPTS -javaagent:path/to/jacocoagent.jar=output=tcpserver"
    /path/to/tomcat/bin/startup.sh

    # Run all tests
    target/functests.jar org.example.tests


Run an optimized subset of tests using previously captured coverage data. e.g. we might want to run a smoke test that takes
no longer than 5 minutes, but achieves the best coverage possible in that time:

    target/functests.jar --optimize-time=5min org.example.tests
