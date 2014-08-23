# How it works

1. You build a JAR file containing your test classes with TestPackage as a dependency. The JAR must point to
`org.testpackage.TestPackage` as the main class which is executed when the JAR is run. The TestPackage maven plugin
helps make assembling this JAR file easier (see below).
2. When the JAR is run, TestPackage orchestrates JUnit to run your test classes. If tests have been run before, it
uses historical data to help prioritize/select which tests to run.
3. Throughout the test execution TestPackage gives nice colorized output on the console, and summarizes causes for
test failures at the end. Also, it generates JUnit XML report files which can be parsed by your CI tool.

![Simple object diagram](http://www.gliffy.com/go/publish/image/6083480/L.png)