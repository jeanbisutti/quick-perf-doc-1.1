# 🚩 Table of contents

[JUnit 4](#junit-4)

[JUnit 5](#junit-5)

[JVM annotations](#JVM-annotations)

[SQL annotations](#SQL-annotations)

[TestNG](#TestNG)

[Project examples](#Project-examples)


# JUnit 4

Add one of the following dependencies below.

**Spring 5**
```xml
<dependency>
  <groupId>org.quickperf</groupId>
  <artifactId>quick-perf-junit4-spring5</artifactId>
  <version>1.1.0</version>
  <scope>test</scope>
</dependency>
```

**Spring 4**
```xml
<dependency>
  <groupId>org.quickperf</groupId>
  <artifactId>quick-perf-junit4-spring4</artifactId>
  <version>1.1.0</version>
  <scope>test</scope>
</dependency>
```

**Spring 3**
```xml
<dependency>
  <groupId>org.quickperf</groupId>
  <artifactId>quick-perf-junit4-spring3</artifactId>
  <version>1.1.0</version>
  <scope>test</scope>
</dependency>
```

**In the test class, use `@RunWith(SpringRunner.class)` instead of`@RunWith(QuickPerfSpringRunner.class)`.**

*QuickPerfSpringRunner* adds QuickPerf features to *SpringRunner* (also called *SpringJUnit4ClassRunner*). <br>

*Java code example with QuickPerfSpringRunner*
```java
	import org.junit.runner.RunWith;
	import quickperf.spring.QuickPerfSpringRunner;

	@RunWith(QuickPerfSpringRunner.class)
	public class AccountTest {

	}
```
# JUnit 5

First, [add QuickPerf dependencies for JUnit 5](JUnit-5#dependencies).

Second, [transform the test into a QuickPerf test with JUnit 5](JUnit-5#quickperf-test).

# JVM annotations

After applying the JUnit 4 or the JUnit 5 configuration below, you can use the [JVM](JVM-annotations) annotations, [*except the JVM profiling annotations*](JVM-annotations#Profile-or-check-your-JVM). To use the last ones, please read [this](JVM-annotations#profile-or-check-your-jvm).

_QuickPerf executes annotations after the loading of the SpringContext._ For example, if you use [ProfileJvm](JVM-annotations#Profile-or-check-your-JVM) annotation, QuickPerf will not profile the Spring context loading.


# SQL annotations

QuickPerf needs to intercept the SQL statements sent to the database. 

The sections hereafter describe several ways to allow QuickPerf to intercept the SQL statements with Spring.

## Spring Boot

With Spring Boot 1, add the following dependency
```xml
<dependency>
  <groupId>org.quickperf</groupId>
  <artifactId>quick-perf-springboot1-sql-starter</artifactId>
  <version>1.1.0</version>
  <scope>test</scope>
</dependency>
```

With Spring Boot 2, use this dependency
```xml
<dependency>
  <groupId>org.quickperf</groupId>
  <artifactId>quick-perf-springboot2-sql-starter</artifactId>
  <version>1.1.0</version>
  <scope>test</scope>
</dependency>
```

If something is missing for some tests using SQL annotations, the console will display configurations to add:
```
Do you use @DataJpaTest? This annotation disables Spring auto-configuration.
So, QuickPerf Spring auto-configuration is disabled.
To allow QuickPerf to intercept the SQL queries, you have two possibilities: 
1) Import QuickPerfSqlConfig (recommended): 
	import org.quickperf.spring.sql.QuickPerfSqlConfig;
	...
	@Import(QuickPerfSqlConfig.class)
	public class TestClass {
2) Force to enable Spring auto-configuration by adding
   @OverrideAutoConfiguration(enabled = true) on the test class

```

## Spring without Spring Boot

With Spring 4 add
```xml
<dependency>
  <groupId>org.quickperf</groupId>
  <artifactId>quick-perf-sql-spring4</artifactId>
  <version>1.1.0</version>
  <scope>test</scope>
</dependency>
```

With Spring 5 add
```xml
<dependency>
  <groupId>org.quickperf</groupId>
  <artifactId>quick-perf-sql-spring5</artifactId>
  <version>1.1.0</version>
  <scope>test</scope>
</dependency>
```

Import `org.quickperf.spring.sql.QuickPerfSqlConfig` configuration on the test class.

### :mag_right: Example
```java
import org.quickperf.spring.sql.QuickPerfSqlConfig;
import org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTest;
import org.springframework.context.annotation.Import;

@RunWith(QuickPerfSpringRunner.class)
@Import(QuickPerfSqlConfig.class)
@DataJpaTest()
public class PlayerRepositoryTest {

}
```

## REST controller without MockMvc

You have to execute the test in a dedicated JVM, for example, using `HeapSize` annotation.

If you don't do this, QuickPerf will display the following message on the console to help you in the configuration:

```
Execute the test in a dedicated JVM by adding @HeapSize(value = ..., unit = AllocationUnit.MEGA_BYTE).
A heap size value around 50 megabytes may allow the test to run.
```

### :mag_right: Example
```java
import football.FootballApplication;
import football.dto.PlayerWithTeamName;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.quickperf.jvm.allocation.AllocationUnit;
import org.quickperf.jvm.annotations.HeapSize;
import org.quickperf.spring.junit4.QuickPerfSpringRunner;
import org.quickperf.sql.annotation.ExpectSelect;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.test.web.client.TestRestTemplate;
import org.springframework.boot.web.server.LocalServerPort;
import org.springframework.core.ParameterizedTypeReference;
import org.springframework.http.HttpMethod;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;

import java.util.List;

import static org.assertj.core.api.Assertions.assertThat;

@RunWith(QuickPerfSpringRunner.class)
@SpringBootTest(classes = {FootballApplication.class}
              , webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT
               )
public class PlayerControllerTest {

    @LocalServerPort
    private int port;

    @Autowired
    private TestRestTemplate restTemplate;

    @ExpectSelect(1)
    @HeapSize(value = 50, unit = AllocationUnit.MEGA_BYTE)
    @Test
    public void should_get_all_players() {

        // GIVEN
        String url = "http://localhost:" + port + "/players";

        // WHEN
        ParameterizedTypeReference<List<PlayerWithTeamName>> paramType = new ParameterizedTypeReference<List<PlayerWithTeamName>>() {};
        ResponseEntity<List<PlayerWithTeamName>> playersResponseEntity = restTemplate
                .exchange(url, HttpMethod.GET, null, paramType);

        // THEN
        assertThat(playersResponseEntity.getStatusCode()).isEqualTo(HttpStatus.OK);

        List<PlayerWithTeamName> players = playersResponseEntity.getBody();
        assertThat(players).hasSize(2);

    }

}
```

## XML configuration

```xml
<bean id="QuickPerfProxyBeanPostProcessor" class = "org.quickperf.spring.sql.QuickPerfProxyBeanPostProcessor" />
```

# TestNG
You can't yet use QuickPerf annotations when Spring is used together with TestNG. If you are interested in this feature, you can help us to develop it. Don't hesitate to leave a comment on [this issue](https://github.com/quick-perf/quickperf/issues/44).
 
# Project examples

[Spring Boot & Junit4](https://github.com/quick-perf/quickperf-examples/tree/master/springboot-junit5)

[Spring Boot & Junit5](https://github.com/quick-perf/quickperf-examples/tree/master/springboot-junit5)

[Spring Boot & Junit5 & Testcontainers](https://github.com/quick-perf/quickperf-examples/tree/master/tc-springboot-junit5)