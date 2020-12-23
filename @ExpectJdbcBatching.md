Verifies that insert, delete, and update statements are processed through JDBC batches of *batchSize* elements.

[JDBC roundtrips can dramatically impact application performance](https://blog.jooq.org/2017/12/18/the-cost-of-jdbc-server-roundtrips/). Batching  of inserts, updates, and deletes statements allow us to reduce the roundtrip number.

You may sometimes think that you are using JDBC batching, but in fact, you are not:  [Article 1](https://abramsm.wordpress.com/2008/04/23/hibernate-batch-processing-why-you-may-not-be-using-it-even-if-you-think-you-are/), [Article 2](https://stackoverflow.com/questions/27697810/hibernate-disabled-insert-batching-when-using-an-identity-identifier).

### :wrench: Elements 
|Name  |Type| Meaning           | Default value  |
| -------- |:---:|:-----------------:|:--------------:|
| batchSize| int |JDBC batch size   |      -         |

batchSize is optional. With no given batch size value, the annotation checks that insert, delete, and update statements are processed through  JDBC batches, but the annotation does not check the batch size.

A 0 batch size means that JDBC batching is disabled.

### :mag_right: Example
```java
    @ExpectJdbcBatching(batchSize = 30)
```
