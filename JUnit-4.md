### ⚠️ **If you use Spring, please go [here](Spring).**

# 🚩 Table of contents
[QuickPerfJUnitRunner](#QuickPerfJUnitRunner)<br>

[SQL annotations](#SQL-annotations)<br>

[Project examples](#project-examples)

# QuickPerfJUnitRunner

*QuickPerfJUnitRunner* adds QuickPerf features to default JUnit runner. <br>

To use it, you have to this dependency

```xml
<dependency>
  <groupId>org.quickperf</groupId>
  <artifactId>quick-perf-junit4</artifactId>
  <version>1.0.1<version>
  <scope>test</scope>
</dependency>
```
With this dependency, you have access to [core](Core-annotations) and [JVM](JVM-annotations) annotations, [*except the JVM profiling annotations*](JVM-annotations#Profile-or-check-your-JVM),.

*Java code example with QuickPerfSpringRunner*
```java
	import org.junit.runner.RunWith;
	import quickperf.junit4.QuickPerfJUnitRunner;

	@RunWith(QuickPerfJUnitRunner.class)
	public class AccountTest {

	}
```

# SQL annotations

In addition to the dependency mentioned in the [QuickPerfJUnitRunner](#QuickPerfJUnitRunner) part, you have to add the dependency below to can use [SQL annotations](SQL-annotations).
```xml
<dependency>
  <groupId>org.quickperf</groupId>
  <artifactId>quick-perf-sql-annotations</artifactId>
  <version>1.0.1</version>
  <scope>test</scope>
</dependency>
```

You also need to build a datasource proxy with *QuickPerfSqlDataSourceBuilder* and use this proxy in your test. 

Look at the example below.

*Java code example using QuickPerfSqlDataSourceBuilder and a SQL annotation*
```java
	import org.junit.runner.RunWith;
	import quickperf.junit4.QuickPerfJUnitRunner;

        import net.ttddyy.dsproxy.support.ProxyDataSource;
        import org.hibernate.jpa.HibernatePersistenceProvider;
        import org.junit.Before;
        import org.quickperf.sql.entities.Book;

        import javax.persistence.EntityManagerFactory;
        import javax.persistence.spi.PersistenceProvider;
        import javax.persistence.spi.PersistenceUnitInfo;
        import javax.sql.DataSource;
        import java.util.HashMap;
        import java.util.Properties;

        import org.quickperf.sql.config.QuickPerfSqlDataSourceBuilder;

        import org.quickperf.sql.annotation.SelectNumber;

	@RunWith(QuickPerfJUnitRunner.class)
	public class bookTest {

          private EntityManagerFactory emf;

          @Before
          public void before() {
             PersistenceProvider persistenceProvider = new HibernatePersistenceProvider();
             PersistenceUnitInfo info = buildPersistenceUnitInfo();
             emf = persistenceProvider.createContainerEntityManagerFactory(info, new HashMap<>());
          }

         private PersistenceUnitInfo buildPersistenceUnitInfo() {
             DataSource baseDataSource = TestDataSourceBuilder.aDataSource().build();
             ProxyDataSource proxyDataSource = QuickPerfSqlDataSourceBuilder.aDataSourceBuilder().buildProxy(baseDataSource);
             Properties hibernateProperties = HibernateConfigBuilder.anHibernateConfig().build();
             return PersistenceUnitInfoBuilder.aPersistenceUnitInfo()
                                              .build(proxyDataSource
                                                   , hibernateProperties
                                                   , Book.class);
         }

         @ExpectMaxSelect(1)
         @Test
         public void should_retrieve_books_from_database() {
         // ... 
        }

}
```

# Project examples
[JVM & JUnit 4](https://github.com/quick-perf/quickperf-examples/tree/master/jvm-junit4)

[Hibernate & JUnit 4](https://github.com/quick-perf/quickperf-examples/tree/master/hibernate-junit4)