# 🚩 Table of contents

[How to promote performance with SQL annotations?](#how-to-promote-performance-with-SQL-annotations)<br>

[Quickstart](#Quickstart)<br>

[Guidelines to use the SQL annotations](#Guidelines-to-use-the-SQL-annotations)

[Testcontainers](#Testcontainers)

[Available SQL annotations](#Available-SQL-annotations)<br>

# How to promote performance with SQL annotations?
To promote performance at the beginning of application development, you can
* **Limit JDBC roundtrips**
  * [***Detect N+1 selects***](Easily-detect-and-fix-N-plus-One-SELECT-with-QuickPerf) by using [@ExpectSelect](./@ExpectSelect), [@ExpectMaxSelect](./@ExpectMaxSelect) or [@DisableSameSelectTypesWithDifferentParamValues](./@DisableSameSelectTypesWithDifferentParamValues)<br> 
  * ***Detect JDBC batching disabled*** by using [@ExpectJdbcBatching](./@ExpectJdbcBatching)
  * ***Detect the same selects*** by using [@DisableSameSelects](./@DisableSameSelects)

     ***[Why limit JDBC roundtrips?](https://blog.jooq.org/2017/12/18/the-cost-of-jdbc-server-roundtrips/)***

* **Limit fetched data**
  * ***Detect too many selected*** columns by using [@ExpectSelectedColumn](./@ExpectSelectedColumn) or [@ExpectMaxSelectedColumn](./@ExpectMaxSelectedColumn)<br><br>
***[Why limit the number of selected columns?](Why-limit-the-number-of-selected-columns)***
* **Reduce SQL queries execution time**
  * **Spot long queries** with [@ExpectMaxQueryExecutionTime](./@ExpectMaxQueryExecutionTime)
  * **Avoid SQL statements without bind parameters**. Detect them by using [@DisableQueriesWithoutBindParameters](./@DisableQueriesWithoutBindParameters)
  * **Avoid SQL statements having a LIKE pattern starting with a wildcard**. Identify them by using [@DisableLikeWithLeadingWildcard](./@DisableLikeWithLeadingWildcard)

* ...


# Quickstart
## Configure QuickPerf for SQL annotations

:point_right: &nbsp;[**Spring (JUnit 4, JUnit 5)**](Spring)   &nbsp; &nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp; &nbsp; &nbsp; :point_right: [**JUnit 4**](JUnit-4)

:point_right: &nbsp;[**Micronaut** &nbsp; *Preview/Example*](https://github.com/quick-perf/quickperf-examples#micronaut) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; :point_right: [**JUnit 5 example**](https://github.com/quick-perf/quickperf-examples/tree/master/hibernate-junit5) 

:point_right: &nbsp;[**Quarkus** &nbsp; *Preview/Example*](https://github.com/quick-perf/quickperf-examples/tree/master/quarkus) &nbsp; &nbsp;&nbsp; &nbsp; &nbsp;&nbsp;:point_right: [**TestNG example**](https://github.com/quick-perf/quickperf-examples/tree/master/jvm-testng)

## Check the configuration
You can add an annotation on a test method to make it fail. For example, add @ExpectSelect(0) on a test method that is supposed to send one or several selects to the database.

## Annotation scopes
You can use SQL annotations with a [global scope](#configure-global-annotations), a class scope, or a [method scope](#apply-sql-annotations-on-test-methods).

## Automatic framework detection
The SQL annotations automatically detect the presence of *Hibernate* and *Spring* frameworks. These annotations can propose solutions to get the expected behavior with these frameworks.

For example, QuickPerf displays the following message when an N+1 select is presumed, and Spring Data JPA is detected:
```
	* With Spring Data JPA, you may fix it by adding
	@EntityGraph(attributePaths = { "..." }) on repository method.
	https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#jpa.entity-graph
```

# Guidelines to use the SQL annotations
[Configure global annotations](#Configure-global-annotations)<br>

[Disable global annotations at the method level](#Disable-global-annotations-at-the-method-level)<br>

[Apply SQL annotations on methods](#apply-sql-annotations-on-test-methods)<br>

[Test early, test often](#test-early-test-often)<br>


## Configure global annotations

You may want to quickly promote performance without adding some SQL annotations on each test method. Or let's suppose that you add QuickPerf to an application having automatic tests. 

Annotations with a [*global scope*](QuickPerf#annotation-scopes), also called *global annotations*, apply to each test. Configuring SQL global annotations can allow you to detect and fix some performance-sensitive properties promptly.

We recommend configuring the following SQL global annotations:

|Annotation                                                                                          |Short description                                              |
| ---------------------------------------------------------------------------------------------------|---------------------------------------------------------------|
|[@DisableSameSelectTypesWithDifferentParamValues](./@DisableSameSelectTypesWithDifferentParamValues)| Disable same SELECT statements with different parameter values => **_[N+1 select detection]((Easily-detect-and-fix-N-plus-One-SELECT-with-QuickPerf))_**|
|[@ExpectJdbcBatching](./@ExpectJdbcBatching)                                                        | JDBC batching is enabled                                      |
|[@ExpectMaxQueryExecutionTime](./@ExpectMaxQueryExecutionTime)                                      | Max query execution time                                      |
|[@DisableStatements](./@DisableStatements)                                                          | Disables java.sql.Statement                                   |
|[@DisableQueriesWithoutBindParameters](./@DisableQueriesWithoutBindParameters)                      | Disables queries without bind variables                       |
|[@DisableLikeWithLeadingWildcard](./@DisableLikeWithLeadingWildcard)                                | Disable like with leading wildcard                            |

A _SqlAnnotationBuilder_ class allows you to configure SQL global annotations easily.


### :mag_right: Java code of recommended SQL global annotations

⚠ The class implementing `SpecifiableGlobalAnnotations` has to be in the `org.quickperf package`.*

```java
package org.quickperf;

import org.quickperf.config.SpecifiableGlobalAnnotations;

import java.lang.annotation.Annotation;
import java.util.Arrays;
import java.util.Collection;

import static org.quickperf.sql.annotation.SqlAnnotationBuilder.*;

public class QuickPerfConfiguration implements SpecifiableGlobalAnnotations {

    public Collection<Annotation> specifyAnnotationsAppliedOnEachTest() {

        return Arrays.asList(

                // Can reveal some N+1 selects
                // https://blog.jooq.org/2017/12/18/the-cost-of-jdbc-server-roundtrips/
                disableSameSelectTypesWithDifferentParamValues()

                // Sometimes, JDBC batching can be disabled with Hibernate:
                // https://abramsm.wordpress.com/2008/04/23/hibernate-batch-processing-why-you-may-not-be-using-it-even-if-you-think-you-are/
                // https://stackoverflow.com/questions/27697810/hibernate-disabled-insert-batching-when-using-an-identity-identifier
              , expectJdbcBatching()

                // https://use-the-index-luke.com/sql/where-clause/searching-for-ranges/like-performance-tuning
              , disableLikeWithLeadingWildcard()

                // Not relevant with an in-memory database used for testing purpose
              , expectMaxQueryExecutionTime(30)

              , disableStatements()

              , disableQueriesWithoutBindParameters()

        );

    }

}
```

## Disable global annotations at the method level

### Disable some recommended global annotations

In some specific cases, you may want to disable some global annotations. You can use the following annotations to disable some [recommended global annotations](#Configure-global-annotations) for some test methods: 

|Annotation                                                                                        |Short description                                                                                                       |
| -------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------|
|[@EnableSameSelectTypesWithDifferentParamValues](./@EnableSameSelectTypesWithDifferentParamValues)|Cancels behavior of [@DisableSameSelectTypesWithDifferentParamValues](./@DisableSameSelectTypesWithDifferentParamValues)|
|[@ExpectJdbcBatching(batchSize=0)](./@ExpectJdbcBatching)                                         |Cancels behavior of [@ExpectJdbcBatching](./@ExpectJdbcBatching)                                                        |
|[@EnableStatements](./@EnableStatements)                                                          |Cancels behavior of [@DisableStatements](./@DisableStatements)                                                          |
|[@EnableQueriesWithoutBindParameters](./@EnableQueriesWithoutBindParameters)                      |Cancels behavior of [@DisableQueriesWithoutBindParameters](./@DisableQueriesWithoutBindParameters)                      |
|[@EnableLikeWithLeadingWildcard](./@EnableLikeWithLeadingWildcard)                                |Cancels behavior of [@DisableLikeWithLeadingWildcard](./@DisableLikeWithLeadingWildcard)                                |


### Temporarily disable the global annotations
Suppose you are developing a new feature, perhaps with the help of *Test-Driven Development (TDD)*. The test may fail because the business property does not work. 
The test may also fail because of failing performance-related properties. 
We think it is easier to focus on the business behavior (the functional property), and after that to work on performance-related properties, to do one step at a time.
You can _temporarily_ disable QuickPerf global annotations by applying [@FunctionalIteration](core-annotations#disablequickperf), or [@DisableQuickPerf](core-annotations#disablequickperf), or [@DisableGlobalAnnotations](core-annotations#disableglobalannotations) at the method level.

## Apply SQL annotations on test methods

In addition to the performance properties verified by the global annotations, you can check others for some test methods.

The annotations added to the test methods can help to document the code. For example, by reading ```@ExpectSelect(1)``` annotation applied on a test method, you know that you expect exactly one select sent to the database.

Among all the SQL annotations, we suggest using the following ones on the test methods: 

|Annotation                                                    |Short description            |
| -------------------------------------------------------------|-----------------------------|
|[@ExpectJdbcQueryExecution](./@ExpectJdbcQueryExecution)      | JDBC query execution number |
|[@ExpectMaxQueryExecutionTime](./@ExpectMaxQueryExecutionTime)| Max query execution time    |                                 
|[@ExpectSelectedColumn](./@ExpectSelectedColumn)              | Selected columns number     |
|[@ExpectUpdatedColumn](./@ExpectUpdatedColumn)                | Updated columns number|
|[@ExpectMaxSelectedColumn](./@ExpectMaxSelectedColumn)        | Max selected columns number |
|[@ExpectMaxUpdatedColumn](./@ExpectMaxUpdatedColumn)          | Max updated columns         |
|[@ExpectSelect](./@ExpectSelect)                              | SELECT number               |
|[@ExpectInsert](./@ExpectInsert)                              | INSERT number               |
|[@ExpectUpdate](./@ExpectUpdate)                              | UPDATE number               |
|[@ExpectDelete](./@ExpectDelete)                              | DELETE number               |

# Testcontainers
[Testcontainers](https://www.testcontainers.org/) is useful to write tests with the same type of database used in production.

QuickPerf can work with Testcontainers.

Several project examples combining Testcontainers and QuickPerf are available [here](https://github.com/quick-perf/quickperf-examples#testcontainers
).

# Available SQL annotations

## All the SQL annotations
<table>
    <tbody>
       <tr>
            <td> <a href="./@ExpectSelect">@ExpectSelect</a> </td>
            <td> <a href="./@ExpectMaxSelect"> @ExpectMaxSelect</a> </td>
       </tr>      
        <tr>
            <td> <a href="./@ExpectSelectedColumn">@ExpectSelectedColumn</a> </td>
            <td> <a href="./@ExpectMaxSelectedColumn"> @ExpectMaxSelectedColumn</a> </td>
       </tr>     
       <tr>
            <td> <a href="./@ExpectUpdate">@ExpectUpdate</a> </td>
            <td> <a href="./@ExpectMaxUpdate">@ExpectMaxUpdate</a> </td>
       </tr>    
       <tr>
            <td> <a href="./@ExpectUpdatedColumn">@ExpectUpdatedColumn</a> </td>
            <td> <a href="./@ExpectMaxUpdatedColumn"> @ExpectMaxUpdatedColumn</a> </td>
       </tr>    
       <tr>
            <td> <a href="./@ExpectInsert">@ExpectInsert</a> </td>
            <td> <a href="./@ExpectMaxInsert"> @ExpectMaxInsert</a> </td>
       </tr>   
       <tr>
           <td> <a href="./@ExpectDelete">@ExpectDelete</a> </td>
           <td> <a href="./@ExpectMaxDelete">@ExpectMaxDelete</a> </td>
       </tr>   
       <tr>
            <td> <a href="./@DisplaySql">@DisplaySql</a> </td>
            <td> <a href="./@DisplaySqlOfTestMethodBody">@DisplaySqlOfTestMethodBody</a> </td>
       </tr>       
       <tr>
            <td> <a href="./@ExpectJdbcBatching">@ExpectJdbcBatching</a> </td>
            <td> <a href="./@ExpectMaxQueryExecutionTime">@ExpectMaxQueryExecutionTime</a> </td>
       </tr>
       <tr>
            <td> <a href="./@DisableStatements">@DisableStatements</a> </td>
            <td> <a href="./@EnableStatements">@EnableStatements</a> </td>
       </tr>
       <tr>
            <td> <a href="./@DisableSameSelects">@DisableSameSelects</a> </td>
            <td> <a href="./@EnableSameSelects">@EnableSameSelects</a> </td>
       </tr>             
       <tr>
            <td> <a href="./@DisableSameSelectTypesWithDifferentParamValues">@DisableSameSelectTypesWithDifferentParamValues</a> </td>
            <td> <a href="./@EnableSameSelectTypesWithDifferentParamValues">@EnableSameSelectTypesWithDifferentParamValues</a> </td>
       </tr>       
       <tr>
            <td> <a href="./@DisableLikeWithLeadingWildcard">@DisableLikeWithLeadingWildcard</a> </td>
            <td> <a href="./@EnableLikeWithLeadingWildcard">@EnableLikeWithLeadingWildcard</a> </td>
       </tr>       
       <tr>
            <td> <a href="./@DisableQueriesWithoutBindParameters">@DisableQueriesWithoutBindParameters</a> </td>
            <td> <a href="./@EnableQueriesWithoutBindParameters">@EnableQueriesWithoutBindParameters</a> </td>
       </tr>
    </tbody>
</table>

## SELECT statements

|Annotation                                                                                |Short description                                              |
| -----------------------------------------------------------------------------------------|---------------------------------------------------------------|
|[@ExpectSelect](./@ExpectSelect)                                                          | SELECT number                                                 |
|[@ExpectMaxSelect](./@ExpectMaxSelect)                                                    | Max SELECT number                                             |
|[@ExpectSelectedColumn](./@ExpectSelectedColumn)                                          | Selected columns number                                       |
|[@ExpectMaxSelectedColumn](./@ExpectMaxSelectedColumn)                                    | Max selected columns number                                   |
|[@DisableSameSelects](./@DisableSameSelects)                                | Disables exactly same SELECT statements                        |
|[@EnableSameSelects](./@EnableSameSelects)                                  | Enables exactly same SELECT statements                         |
|[@DisableSameSelectTypesWithDifferentParamValues](./@DisableSameSelectTypesWithDifferentParamValues)| Disables same SELECT statements with different parameter values|
|[@EnableSameSelectTypesWithDifferentParamValues](./@EnableSameSelectTypesWithDifferentParamValues)  | Enables same SELECT statements with different parameter values |

## INSERT statements

|Annotation                            |Short description       |
| -------------------------------------|------------------------|
|[@ExpectInsert](./@ExpectInsert)      | INSERT number          |
|[@ExpectMaxInsert](./@ExpectMaxInsert)| Max INSERT number      |

## DELETE statements

|Annotation                            |Short description |
| -------------------------------------|------------------|
|[@ExpectDelete](./@ExpectDelete)      | DELETE number    |
|[@ExpectMaxDelete](./@ExpectMaxDelete)| Max DELETE number|

## UPDATE statements

|Annotation                                          |Short description          | 
| ---------------------------------------------------|---------------------------|
|[@ExpectUpdate](./@ExpectUpdate)                    | UPDATE number             |
|[@ExpectMaxUpdate](./@ExpectMaxUpdate)              | Max UPDATE number         |
|[@ExpectUpdatedColumn](./@ExpectUpdatedColumn)      | Updated columns number|
|[@ExpectMaxUpdatedColumn](./@ExpectMaxUpdatedColumn)| Max updated columns number    |

## Debug

|Annotation                                                  |Short description                        |
| -----------------------------------------------------------|-----------------------------------------|
|[@DisplaySql](./@DisplaySql)                                | Displays SQL                             |
|[@DisplaySqlOfTestMethodBody](./@DisplaySqlOfTestMethodBody)| Displays SQL executed in the test method body|

You can also use [@DisplayAppliedAnnotations](Core-annotations#DisplayAppliedAnnotations) in debug activity.

## Other

|Annotation                                                                     |Short description                       |
| ------------------------------------------------------------------------------|----------------------------------------|
|[@ExpectJdbcQueryExecution](./@ExpectJdbcQueryExecution)                       | JDBC query execution number            |
|[@ExpectMaxJdbcQueryExecution](./@ExpectMaxJdbcQueryExecution)                 | Max JDBC query execution number        |
|[@ExpectJdbcBatching](./@ExpectJdbcBatching)                                   | JDBC batching is enabled               |
|[@ExpectMaxQueryExecutionTime](./@ExpectMaxQueryExecutionTime)                 | Max query execution time               |
|[@DisableStatements](./@DisableStatements)                                     | Disables java.sql.Statement            |
|[@EnableStatements](./@EnableStatements)                                       | Enablesjava.sql.Statement              |
|[@DisableLikeWithLeadingWildcard](./@DisableLikeWithLeadingWildcard)           | Disables like with leading wildcard    |
|[@EnableLikeWithLeadingWildcard](./@EnableLikeWithLeadingWildcard)             | Enables like with leading wildcard     |
|[@DisableQueriesWithoutBindParameters](./@DisableQueriesWithoutBindParameters) | Disables queries without bind variables|
|[@EnableQueriesWithoutBindParameters](./@EnableQueriesWithoutBindParameters)   | Enables queries without bind variables |

