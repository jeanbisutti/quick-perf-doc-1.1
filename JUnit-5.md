# 🚩 Table of contents
[Dependencies](#Dependencies) <br> 

[QuickPerf test](#QuickPerf-test)

[Dynamic Tests](#Dynamic-Tests) <br> 

[Project examples](#Project-examples)


## Dependencies
⚠️ **_JUnit 5 >= 5.6.0 is required_**

You can use JUnit 5 and QuickPerf [BOM files](https://dzone.com/articles/the-bill-of-materials-in-maven).

In the case of Maven, you can add the following dependency management:

```xml
 <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.junit</groupId>
                <artifactId>junit-bom</artifactId>
                <version>5.7.0</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <dependency>
                <groupId>org.quickperf</groupId>
                <artifactId>quick-perf-bom</artifactId>
                <version>1.1.0</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
 </dependencyManagement>
```

The following JUnit 5 dependencies are required: 
```xml
  <dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter-engine</artifactId>
    <scope>test</scope>
  </dependency>
  <dependency>
     <groupId>org.junit.platform</groupId>
     <artifactId>junit-platform-launcher</artifactId>
     <scope>test</scope>
  </dependency>
```
If you get a `java.lang.NoClassDefFoundError: org/junit/platform/launcher/core/LauncherDiscoveryRequestBuilder`, then the `junit-platform-launcher `dependency is missing.


You also need the following QuickPerf dependency:
```xml
 <dependency>
     <groupId>org.quickperf</groupId>
     <artifactId>quick-perf-junit5</artifactId>
     <scope>test</scope>
 </dependency>
```

You can now use the [core](Core-annotations) and [JVM](JVM-annotations) annotations, [*except the JVM profiling annotations*](JVM-annotations#Profile-or-check-your-JVM), by transforming the test class into a QuickPerf test class (see just below).

💡 Want to use [**SQL annotations**](SQL-annotations) with **Spring**? Please read [this](Spring#spring-and-junit-5).

## QuickPerf test

A QuickPerf test executes QuickPerf annotations. With JUnit 5, you can transform your tests into QuickPerf tests in two ways.

### QuickPerfTest annotation
Add `@QuickPerfTest` on the test classes.

### Automatically register QuickPerf extension
JUnit 5 can automatically register QuickPerf extension. So, with this mechanism, you don't have to add `QuickPerfTest` annotation on the test methods.

To enable the automatic extension registering, add a `junit-platform.properties` file in `src/test/resources`. After that, add the following line to this file:
```
junit.jupiter.extensions.autodetection.enabled=true
```

## Dynamic Tests
With JUnit 5, you can define a test method that generates other tests. This particular method is a test factory, creating dynamic tests.

You can use Quickperf annotations on test factory methods, be aware that in this case, 
the annotation applies to all dynamic tests created by the test factory method, not on the test factory method on itself.

The following example will fail:

```java
@ExpectSelect(2)
@TestFactory
public List<DynamicTest> execute_two_select_but_five_select_expected() {
    List<DynamicTest> dynamicTests = new ArrayList<>();
    dynamicTests.add(DynamicTest.dynamicTest("Dynamic test 1", () -> {
        Query query = entityManager.createQuery("FROM " + Book.class.getCanonicalName());
        query.getResultList();
    }));
    dynamicTests.add(DynamicTest.dynamicTest("Dynamic test 2", () -> {
        Query query = entityManager.createQuery("FROM " + Person.class.getCanonicalName());
        query.getResultList();
    }));
    return dynamicTests;
}
```

The `@ExpectSelect(2)` annotation will apply to both dynamic tests; as each generates only one query, you will have two failures (one for each test).
The correct usage would have been `@ExpectSelect(1)`.


## Project examples

[JVM & JUnit 5](https://github.com/quick-perf/quickperf-examples/tree/master/jvm-junit5)

[Hibernate & JUnit 5](https://github.com/quick-perf/quickperf-examples/tree/master/hibernate-junit5)

[Spring Boot & JUnit 5](https://github.com/quick-perf/quickperf-examples/tree/master/springboot-junit5)

[Spring Boot & JUnit 5 & Testcontainers](https://github.com/quick-perf/quickperf-examples/tree/master/tc-springboot-junit5)

[Micronaut & Hibernate](https://github.com/quick-perf/quickperf-examples/tree/master/micronaut-hibernate-jpa)

[Micronaut Data](https://github.com/quick-perf/quickperf-examples/tree/master/micronaut-data-jdbc)

[Quarkus](https://github.com/quick-perf/quickperf-examples/tree/master/quarkus)