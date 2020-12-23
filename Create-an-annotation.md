On this page, you will learn the principles to create a QuickPerf annotation. After reading it, you could develop a QuickPerf annotation in the QuickPerf project or your project. Also, you will learn how to create your Maven module defining your custom QuickPerf annotations.

# 🚩 Table of contents
[Principles to create a QuickPerf annotation](#Principles-to-create-a-QuickPerf-annotation)
* [Declare an SPI implementation](#Declare-an-SPI-implementation)
* [Implement QuickPerfConfigLoader](#Implement-QuickPerfConfigLoader)
* [Define the configuration of an annotation](#Define-the-configuration-of-a-given-annotation)

[Create a core, a JVM or a SQL annotation](#Create-a-core-a-JVM-or-a-SQL-annotation)

[Define custom annotations in a specific Maven module](#Define-custom-annotations-in-a-specific-Maven-module)

[Test your new annotation](#Test-your-new-annotation)
* [Test your SQL annotation](#test-your-sql-annotation)
* [Test your JVM annotation](#test-your-jvm-annotation)

[Debug an annotation](#Debug-an-annotation)

# Principles to create a QuickPerf annotation

*The code examples below come from [sql-annotations Maven module](https://github.com/quick-perf/quickperf/tree/master/sql-annotations).*

## Declare an SPI implementation
QuickPerf uses the Service Provider Interface ([SPI](https://docs.oracle.com/javase/tutorial/sound/SPI-intro.html)) to dynamically load the configurations of annotations.

A file named *org.quickperf.config.library.QuickPerfConfigLoader* has to be in src/main/resources/META-INF/services:

<img src="https://github.com/quick-perf/doc/blob/master/doc/images/QuickPerfConfigLoader.png">
<br><br>

The Content of org.quickperf.config.library.QuickPerfConfigLoader file is here: ```org.quickperf.sql.config.library.SqlConfigLoader```

## Implement QuickPerfConfigLoader

An implementation of QuickPerfConfigLoader has to define three methods:
* *loadAnnotationConfigs()*: returns the configurations of annotations
* *loadRecorderExecutionOrdersBeforeTestMethod()*: returns the recorder priorities *before* the execution of a test method
* *loadRecorderExecutionOrdersAfterTestMethod()*: returns the recorder priorities *after* the execution of a test method

```java
public class SqlConfigLoader implements QuickPerfConfigLoader {

 @Override
    public Collection<AnnotationConfig> loadAnnotationConfigs() {
        return Arrays.asList(
                // ..
                , SqlAnnotationsConfigs.DISABLE_EXACTLY_SAME_SQL_SELECTS
                // ...
        );
    }

    @Override
    public Collection<RecorderExecutionOrder> loadRecorderExecutionOrdersBeforeTestMethod() {
        return Arrays.asList(
                  new RecorderExecutionOrder(PersistenceSqlRecorder.class, 2000)
        );
    }

    @Override
    public Collection<RecorderExecutionOrder> loadRecorderExecutionOrdersAfterTestMethod() {
        return Arrays.asList(
                 new RecorderExecutionOrder(PersistenceSqlRecorder.class, 7000)
        );
    }

}
```

## Define the configuration of a given annotation
The configuration of an annotation is defined with the help of an instance of AnnotationConfig.Builder().

```java
class SqlAnnotationsConfigs {

	static final AnnotationConfig DISABLE_EXACTLY_SAME_SQL_SELECTS = new AnnotationConfig.Builder()
			.perfRecorderClass(PersistenceSqlRecorder.class)
			.perfMeasureExtractor(HasExactlySameSelectExtractor.INSTANCE)
			.perfIssueVerifier(HasExactlySameSelectVerifier.INSTANCE)
			.build(DisableSameSelects.class);
      // ...
}
```
Let's explain the methods used in the example above:
* *perfRecorderClass*: to specify a performance recorder, for example the set of SQL statements sent to database during the execution of the test method body 
* *perfMeasureExtractor*: to define the way a performance measure is extracted from the recorder, for example if there exists exactly same SELECT statements sent to the database
* *perfIssueVerifier*: to evaluate if a performance issue exists
* *build*: the annotation for which the annotation configuration is built

You can use *testHasToBeLaunchedInASpecificJvm()* method to specify that the test method has to be executed in a specific JVM. A *testHasToBeLaunchedInASpecificJvm(AnnotationToJvmOptionConverter annotationToJvmOptionConverter)* method is also available to add some options to the JVM. 

An example for @Xmx:
```java
   static final AnnotationConfig XMX = new AnnotationConfig.Builder()
            .testHasToBeLaunchedInASpecificJvm(XmxAnnotToJvmOptionConverter.INSTANCE)
            .build(Xmx.class);
```
The code of XmxAnnotToJvmOptionConverter can be found [here](https://github.com/quick-perf/quickperf/blob/master/jvm-annotations/src/main/java/org/quickperf/jvm/config/library/XmxAnnotToJvmOptionConverter.java).

# Create a core, a JVM or a SQL annotation
You need to:
1) [Define the configuration of the annotation](#Define-the-configuration-of-a-given-annotation)
2) Declare a new annotation configuration in loadAnnotationConfigs() method of [QuickPerfConfigLoader implementation](#Implement-QuickPerfConfigLoader)
3) If you have defined a new recorder type, you have to complete the loadRecorderExecutionOrdersBeforeTestMethod()
loadRecorderExecutionOrdersAfterTestMethod() methods of [QuickPerfConfigLoader implementation](#Implement-QuickPerfConfigLoader)

## Steps to follow to add a SQL annotation to QuickPerf
1) Add the SQL annotation [here](https://github.com/quick-perf/quickperf/tree/master/sql/sql-annotations/src/main/java/org/quickperf/sql/annotation)
2) Add a test [here](https://github.com/quick-perf/quickperf/tree/master/sql/sql-memory-test/src/test/java)
3) Add a new package in [sql-annotations Maven module](https://github.com/quick-perf/quickperf/tree/master/sql/sql-annotations/src/main/java/org/quickperf/sql)
4) In this new package add a performance measure extractor and a performance issue verifier. You can look at [these examples](https://github.com/quick-perf/quickperf/tree/master/sql/sql-annotations/src/main/java/org/quickperf/sql/insert).
5) Add an AnnotationConfig [here](https://github.com/quick-perf/quickperf/blob/master/sql/sql-annotations/src/main/java/org/quickperf/sql/config/library/SqlAnnotationsConfigs.java) (most of the time, you are going to use the `PersistenceSqlRecorder.class` performance recorder class)
6) Use the new AnnotationConfig in `loadAnnotationConfigs()` method of [SqlConfigLoader](https://github.com/quick-perf/quickperf/blob/master/sql/sql-annotations/src/main/java/org/quickperf/sql/config/library/SqlConfigLoader.java) 
7) Complete the measure extractor and the performance issue verifier to make the test pass. Refactor to clean the code.
8) In [SqlAnnotationBuilder](https://github.com/quick-perf/quickperf/blob/master/sql/sql-annotations/src/main/java/org/quickperf/sql/annotation/SqlAnnotationBuilder.java), add a method to can build the new annotation. This method could be used to define the annotation with [a global scope](QuickPerf#annotation-scopes).

# Define custom annotations in a specific Maven module
You can develop custom QuickPerf annotations and gather them in a Maven module. To develop the annotations, you can follow the [principles described above](#Principles-to-create-a-QuickPerf-annotation). To use them, you have to use the developed Maven dependency together with one QuickPerf dependency (see [here](JUnit-4) for JUnit4 or [here](Spring) for Spring). Don't hesitate to propose a [feature request](https://github.com/quick-perf/quickperf/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=) and a PR to integrate your annotations in the QuickPerf project!


# Test your new annotation

## Test your SQL annotation
 * [With a memory database](https://github.com/quick-perf/quickperf/tree/master/sql/sql-memory-test/src/test/java)
 * [With PostgreSQL](https://github.com/quick-perf/quickperf/tree/master/sql/sql-testcontainers-test/postgresql-test/src/test/java/org/quickperf/sql)
 * [With MariaDB](https://github.com/quick-perf/quickperf/tree/master/sql/sql-testcontainers-test/mariadb-test/src/test/java/org/quickperf/sql)

## Test your JVM annotation
 * [With JUnit 4](https://github.com/quick-perf/quickperf/tree/master/junit4/junit4-12-jvm-test/src/test/java/org/quickperf/jvm)
 * [With JUnit 5](https://github.com/quick-perf/quickperf/tree/master/junit5/junit5-jvm-test/src/test/java/org/quickperf/junit5/jvm)
 * [With TestNG](https://github.com/quick-perf/quickperf/tree/master/testng/testng-jvm-test/src/test/java/org/quickperf/testng/jvm)

# Debug an annotation
You can add [@DebugQuickPerf](Core-annotations#DebugQuickPerf) on a test method to get debug data.