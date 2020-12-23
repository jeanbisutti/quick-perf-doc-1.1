
# 🚀 New features and improvements

## General
* [Add configurable verbosity of the annotation display](./Limit-console-verbosity) [*@kanedafromparis*](https://github.com/kanedafromparis) (idea) & [*@FTarfasse*](https://github.com/FTarfasse) (implementation)
* When an unexpected QuickPerf exception happens, display a link on the console to create a pre-filled Github issue [*@jeanbisutti*](https://github.com/jeanbisutti)
* Improve Readme [*@kanedafromparis*](https://github.com/kanedafromparis)

## Core
* Add Javadoc to core annotations [*@FTarfasse*](https://github.com/FTarfasse)

## JVM
* Add Javadoc to JVM annotations [*@FTarfasse*](https://github.com/FTarfasse)
* Ability to enable JDK Flight Recording with OpenJDK JDK 8 [*@jeanbisutti*](https://github.com/jeanbisutti)
* Display JVM info in the error report when QuickPerf can't enable JFR profiling [*@jeanbisutti*](https://github.com/jeanbisutti)
* Fix wording: Java Mission Control replaced with JDK Mission Control [*@jeanbisutti*](https://github.com/jeanbisutti)
* Bump JMC from 7.0.0 to 7.1.1 [*@jeanbisutti*](https://github.com/jeanbisutti)
* Add heap allocation non-regression test for JUnit 4.5 [*@jeanbisutti*](https://github.com/jeanbisutti)

## SQL
* Add automatic configuration advice: when something is not well configured to have working SQL annotations, QuickPerf displays configuration advice on the console [*@jeanbisutti*](https://github.com/jeanbisutti)
* Add [Spring Boot 1 and Spring Boot 2 starters](./Spring#spring-boot) to ease configuration [*@jeanbisutti*](https://github.com/jeanbisutti)
* Add [QuickPerfSqlConfig](./Spring#spring-without-spring-boot) to ease configuration [*@jeanbisutti*](https://github.com/jeanbisutti)
* Add [ExpectJdbcQueryExecution](./@ExpectJdbcQueryExecution) and [ExpectMaxJdbcQueryExecution](./@ExpectMaxJdbcQueryExecution)  annotations [*@jeanbisutti*](https://github.com/jeanbisutti)
* Add [DisableStatements](./@DisableStatements) and [EnableStatements](./@EnableStatements) annotations [*@jeanbisutti*](https://github.com/jeanbisutti)
* Add [ExpectMaxInsert](./@ExpectMaxInsert) annotation [*@MrSnix*](https://github.com/MrSnix)
* Add [ExpectMaxDelete](./@ExpectMaxDelete) annotation [*@navkumar258*](https://github.com/navkumar258)
* Add [ExpectMaxUpdate](./@ExpectMaxUpdate)  annotation [*@FTarfasse*](https://github.com/FTarfasse)
* ExpectSelect and ExpectMaxSelect annotations: don't display N+1 select message if there are only the same selects [*@jeanbisutti*](https://github.com/jeanbisutti) 
* Add suggestion to enable JDBC batching with Hibernate or Spring Boot [*@MrSnix*](https://github.com/MrSnix) & [*@jeanbisutti*](https://github.com/jeanbisutti) 
* The error report now contains the database exception and the executed SQL if an improper SQL is executed. [*@jeanbisutti*](https://github.com/jeanbisutti) 
* Add Javadoc to SQL annotations [*@FTarfasse*](https://github.com/FTarfasse)
* Improve reporting of ExpectMaxQueryExecutionTime annotation [*@FTarfasse*](https://github.com/FTarfasse)
* Add comment element to EnableQueriesWithoutBindParameters annotation [*@jeanbisutti*](https://github.com/jeanbisutti)
* Provide Hibernate / Testcontainers / QuickPerf example [*@kanedafromparis*](https://github.com/kanedafromparis) [See example repository](https://github.com/quick-perf/quickperf-examples)
* Provide Spring boot / Testcontainers / QuickPerf example [*@kanedafromparis*](https://github.com/kanedafromparis) [See example repository](https://github.com/quick-perf/quickperf-examples)
* Add non-regression tests with PostgreSQL and MariaDB with the help of Testcontainers [*@jeanbisutti*](https://github.com/jeanbisutti)
* Bump datasource-proxy dependency from 1.6 to 1.7 [*@jeanbisutti*](https://github.com/jeanbisutti)
* Fix N+1 select issue typo [*@jeanbisutti*](https://github.com/jeanbisutti)
 
## JUnit 5
* [QuickPerf extension can now be automatically registered](JUnit-5#automatically-register-quickperf-extension) [*@jeanbisutti*](https://github.com/jeanbisutti)
* [Add support for dynamic tests](JUnit-5#dynamic-tests) [*@loicmathieu*](https://github.com/loicmathieu) 

## JUnit 5 & JVM
* Execute only once @BeforeEach and @AfterEach code (from the JVM executing the test method) [*@loicmathieu*](https://github.com/loicmathieu) 

## Build
* Add JDK 15 build [*@jeanbisutti*](https://github.com/jeanbisutti)
* Ability to deploy with a release profile without having to install with default profile previously [*@hboutemy*](https://github.com/hboutemy)
  
# 🐛 Bug fixes

## JVM
* Fix heap allocation measure for JUnit 4.13 and JUnit 4.13.1 [*@jeanbisutti*](https://github.com/jeanbisutti) 

## SQL
* @DisableQueriesWithoutBindParameters: add tests and fix them for SQL statements containing between, update, insert, like and for nested statements [*@MohamedKaraga*](https://github.com/MohamedKaraga)
* Fix ClassCastException when execute method is called on Statement [*@jeanbisutti*](https://github.com/jeanbisutti) 
* With a Spring application SQL requests might not be intercepted _(bug found with https://github.com/jeanbisutti/tutorials/tree/master/jhipster/jhipster-microservice/dealer-app_) [*@jeanbisutti*](https://github.com/jeanbisutti) 

#  ⚠️ Breaking changes
## Core
* Remove microSeconds and nanoSeconds elements from ExpectMaxExecutionTime annotation [*@loicmathieu*](https://github.com/loicmathieu) & [*@jeanbisutti*](https://github.com/jeanbisutti) 

## JVM
* To be able to use the ProfileJvm and ExpectNoJvmIssue annotations, the following dependency has to be added:
```xml
<dependency>
   <groupId>org.quickperf</groupId>
   <artifactId>quick-perf-jfr-annotations</artifactId>
   <version>1.0.0</version>
   <scope>test</scope>
</dependency>
```
These dependencies are also moved to `org.quickperf.jvm.jfr.annotation` package. 

## SQL
* `SqlAnnotationBuilder.disableSameSelectTypesWithDifferentParams()` renamed  `SqlAnnotationBuilder.disableSameSelectTypesWithDifferentParamValues()` [*@jeanbisutti*](https://github.com/jeanbisutti)
* Set default value from zero to one for ExpectSelect, ExpectInsert, ExpectUpdate, ExpectDelete, ExpectUpdatedColumn, and ExpectSelectedColumn annotations [*@jeanbisutti*](https://github.com/jeanbisutti)
* DisableExactlySameSelects annotation renamed DisableSameSelects and EnableExactlySameSelects annotation renamed EnableSameSelects [*@jeanbisutti*](https://github.com/jeanbisutti)
* Remove DisableCrossJoin and EnableCrossJoin annotations because modern databases replace the cross join [*@jeanbisutti*](https://github.com/jeanbisutti)
* For ExpectMaxQueryExecutionTime annotation, the `value` and `unit` elements are replaced with a `thresholdInMilliSeconds` element. [See this commit](https://github.com/quick-perf/quickperf/commit/bbb561bd1eeeeab228e12688b14a571bfd9592b9)

## JUnit 4
* JUnit 4 dependency has now to be provided [*@jeanbisutti*](https://github.com/jeanbisutti) 

## JUnit 5
* `junit-jupiter-engine` and `junit-platform-launcher` dependencies have now to be provided  [*@jeanbisutti*](https://github.com/jeanbisutti) & [*@loicmathieu*](https://github.com/loicmathieu)

# 👍 Contributors
[@jeanbisutti](https://github.com/jeanbisutti) <br>

[@FTarfasse](https://github.com/FTarfasse) <br>

[@hboutemy](https://github.com/hboutemy) <br>

[@kanedafromparis](https://github.com/kanedafromparis) <br>

[@loicmathieu](https://github.com/loicmathieu) <br>

[@MohamedKaraga](https://github.com/MohamedKaraga) <br>

[@MrSnix](https://github.com/MrSnix) <br>

[@navkumar258](https://github.com/navkumar258)