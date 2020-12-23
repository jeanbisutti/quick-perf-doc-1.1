The test will fail if a query without bind parameters is found.<br>

Bind parameters is an essential feature to prevent SQL injections and can help improve performance: <br>
- https://blogs.oracle.com/sql/improve-sql-query-performance-by-using-bind-variables <br>
- https://use-the-index-luke.com/sql/where-clause/bind-parameters
- https://dzone.com/articles/why-sql-bind-variables-are-important-for-performan

In the case of skewed data, sharing a query execution plan with several executions may not be the best choice, as explained in [this article](https://medium.com/@FranckPachot/postgresql-bind-variable-peeking-fb4be4942252).

We recommend configuring _DisableQueriesWithoutBindParameters_ annotation with a [global scope](QuickPerf#annotation-scopes) to prevent SQL injections and because, most of the time, bind parameters have a positive effect on performance.

You can disable the global scope _DisableQueriesWithoutBindParameters_ by adding [_EnableQueriesWithoutBindParameters_](./@EnableQueriesWithoutBindParameters) annotation on specific methods.

### :mag_right: Example
Configuration of _DisableQueriesWithoutBindParameters_ with a [global scope](QuickPerf#annotation-scopes).

⚠️ *The class implementing `SpecifiableGlobalAnnotations` has to be in the `org.quickperf` package.*

```java
package org.quickperf;

import org.quickperf.config.SpecifiableGlobalAnnotations;
import org.quickperf.sql.annotation.SqlAnnotationBuilder;

import java.lang.annotation.Annotation;
import java.util.Arrays;
import java.util.Collection;

public class QuickPerfConfiguration implements SpecifiableGlobalAnnotations {

    public Collection<Annotation> specifyAnnotationsAppliedOnEachTest() {

        return Arrays.asList(
                SqlAnnotationBuilder.disableQueriesWithoutBindParameters()
        );

    }

}
```
