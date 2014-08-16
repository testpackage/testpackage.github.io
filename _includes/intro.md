

TestPackage is a simple Java library designed to make it easy to bundle JUnit tests in standalone executable JAR form, rather than the traditional model of running them from an Ant/Maven/Gradle build script.


It is *not intended for running unit tests*, which clearly need to be built around the units of code under test (on the same classpath). However, for situations like integration tests, functional tests and smoke tests, inclusion in a build script adds unnecessary complexity and makes some opportunities harder to grasp.


Additionally, TestPackage aims to provide more useful and intuitive output than other JUnit runners give, for example making it easier to spot assertion errors and root causes of exceptions at a glance.