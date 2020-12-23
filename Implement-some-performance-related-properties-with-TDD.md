_We could add many things to this document! It's a **draft** version. Don't hesitate to give feedback to jean.bisutti@gmail.com_.


# 🚩 Table of contents
[Introduction](#Introduction) <br><br>
[A first example combining business and performance feedback loops](#a-first-example-combining-business-and-performance-feedback-loops) <br><br>
[Evaluate several performance-related properties](#evaluate-several-performance-related-properties) <br><br>
[Workflow including global performance checks](#workflow-including-global-performance-checks)  <br><br>
[Conclusion](#Conclusion) <br><br>

# Introduction

TDD is about, from [@mirjam_diala](https://twitter.com/mirjam_diala):

![](https://pbs.twimg.com/media/EQFLqoVUUAAH3tO?format=jpg&name=small)

At first, we will implement business, functional, behavior with the help of *Test-Driven Development* (TDD). In a second step, we will use TDD to implement some persistence performance properties.


![](https://github.com/quick-perf/doc/blob/master/doc/images/TDD.jpg)

# A first example combining business and performance feedback loops

**Write a failing test on business behavior**

Let's imagine that you want to retrieve some data stored in a database.

Let's write a failing test!

```java
    @Test
    public void should_find_all_players() {

        PlayerRepository playerRepository = new PlayerRepository(entityManager);

        List<Player> players = playerRepository.findAll();

        assertThat(players).hasSize(2);

    }
```

```
java.lang.AssertionError: 
Expected size:<2> but was:<0>
```

The test fails because the implementation returns an empty list:
```java
public class PlayerRepository {

    private final EntityManager entityManager;

    public PlayerRepository(EntityManager entityManager) {
        this.entityManager = entityManager;
    }

    public List<Player> findAll() {
        return Collections.emptyList();
    }

}
```

<br>

**Implement business behavior**

Now let's write code to have a green test and to have a working business behavior:

```java
public class PlayerRepository {

    private final EntityManager entityManager;

    public PlayerRepository(EntityManager entityManager) {
        this.entityManager = entityManager;
    }

    public List<Player> findAll() {
        return entityManager.createQuery("FROM Player").getResultList();
    }

}
```

Now we have a green test! :)

<br>

**Refactor the code**

[Let's clean up a little the code to have something a little more readable](https://blog.cleancoder.com/uncle-bob/2014/12/17/TheCyclesOfTDD.html):

```java
public class PlayerRepository {

    private final EntityManager entityManager;

    public PlayerRepository(EntityManager entityManager) {
        this.entityManager = entityManager;
    }

    public List<Player> findAll() {
        Query loadPlayerQuery = entityManager.createQuery("FROM Player");
        return loadPlayerQuery.getResultList();
    }

}
```

The test is still green!

<br>

**Evaluate a resource usage property: number of JDBC roundtrips**

Let's now evaluate a performance-related property!
We expect to retrieve the data with one select statement, and so one JDBC roundtrip.

Let's check this with the help of an `ExpectSelect(1)` QuickPerf annotation:

```java
@QuickPerfTest
public class PlayerRepositoryTest {
    
    @ExpectSelect(1)
    @Test
    public void should_find_all_players() {

        PlayerRepository playerRepository = new PlayerRepository(entityManager);

        List<Player> players = playerRepository.findAll();

        assertThat(players).hasSize(2);

    }
```

Let's restart the test. Now the test is failing!
```
[PERF] You may think that <1> select statement was sent to the database
       But in fact <3>...

💣  You may have even more select statements with production data.
Be careful with the cost of JDBC server roundtrips: https://blog.jooq.org/2017/12/18/the-cost-of-jdbc-server-roundtrips/
```

**Implement a resource usage property: number of JDBC roundtrips**

The code should be modified to have only one select sent to the database and get a green test.

<br>

**Refactor**

<br>

# Evaluate several performance-related properties

After, we could check other performance properties, as [the number of selected columns](Why-limit-the-number-of-selected-columns): 

```java
@QuickPerfTest
public class PlayerRepositoryTest {

    @ExpectSelectedColumn(3)
    @ExpectSelect(1)
    @Test
    public void should_find_all_players() {

        PlayerRepository playerRepository = new PlayerRepository(entityManager);

        List<Player> players = playerRepository.findAll();

        assertThat(players).hasSize(2);

    }
```


After that, we could for example check the query execution time with a production-like database:

``` java
    @ExpectMaxQueryExecutionTime(value = 20, unit = TimeUnit.MILLISECONDS)
    @ExpectSelectedColumn(3)
    @ExpectSelect(1)
    @Test
    public void should_find_all_players() {

        PlayerRepository playerRepository = new PlayerRepository(entityManager);

        List<Player> players = playerRepository.findAll();

        assertThat(players).hasSize(2);

    }
 ```

_It is worth noting that the performance properties do not systematically fail after adding the annotation, unlike the functional properties._



_Performance properties are evaluated (and perhaps fixed) one after the other._

By assessing and fixing some performance properties, we can promote performance and scalability at the beginning of application development. These performance properties are like quality attributes added to the feature. In the previous example, we could think that @ExpectSelect(1) and @ExpectMaxQueryExecutionTime are more priority performance quality attributes than @ExpectSelectedColumn. Indeed, @ExpectSelect(1) allows detecting N+1 selects that could lead to many JDBC roundtrips with production data. @ExpectMaxQueryExecutionTime is essential to avoid long queries. Later, we could decrease the maximum permitted query execution time. The number of selected columns can [make the query execution time greater](https://use-the-index-luke.com/sql/clustering/index-only-scan-covering-index) and increase the memory consumed and the IO. In some situations, these two last things could be considered first with a low priority even though it is a waste of resources.  

# Workflow including global performance checks
    
If a functional property is broken during an iteration, you can temporarily disable the performance properties' verification by adding _@FunctionalIteration_ on your test method. We try to do one thing at a time; that is to say, we implement the functional behavior, and after that, we check the performance properties.

We could temporarily disable the [global performance checks](SQL-annotations#configure-global-annotations) with the addition of @DisableGlobalAnnotations on the test method. _We have to explain why in the frame of a TDD workflow... Stay tuned!_

# Conclusion