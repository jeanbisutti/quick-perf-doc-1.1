With this annotation, the test will fail at least one request exceeds the max expected query execution time.

If you use a memory database to test the persistence layer, the usage of this annotation does not make sense.

### :wrench: Elements 
|Name                    |Type        | Meaning                                              | 
| ---------------------- |:----------:|:----------------------------------------------------:|
| thresholdInMilliSeconds| int        |Max allowed query execution time value in milliseconds| 
