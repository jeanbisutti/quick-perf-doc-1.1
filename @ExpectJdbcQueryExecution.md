The ExpectJdbcQueryExecution</code> annotation verifies the number of JDBC query executions (_executeQuery_, _executeUpdate_, _execute_, _executeLargeUpdate_, _executeBatch_, _executeLargeBatch_). See <a href="http://ttddyy.github.io/datasource-proxy/docs/current/user-guide/index.html"> datasource-proxy user guide</a> about JDBC query executions.

Each JDBC execution triggers one or several JDBC roundtrips. For example, if a SELECT statement returns a row number greater than JDBC fetch size, the retrieval of all rows will need several JDBC roundtrips.
 
See also the [ExpectMaxJdbcQueryExecution](./@ExpectMaxJdbcQueryExecution) annotation.

### :wrench: Elements 
|Name      |Type | Meaning             | Default value  |
| -------- |:---:|:-------------------:|:--------------:|
| value    | int |JDBC query execution |        1      |

### :mag_right: Example
```java
    @ExpectJdbcQueryExecution(2)
    @Test
    public void execute_2_inserts() {	
     //...
    }
```