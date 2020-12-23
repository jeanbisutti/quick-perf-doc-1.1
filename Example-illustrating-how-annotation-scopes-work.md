To configure global annotations, i.e., annotations applying on each test, create a class implementing `SpecifiableGlobalAnnotations` and locate it in `org.quickperf` package:
```java
package org.quickperf;

import org.quickperf.config.SpecifiableGlobalAnnotations;
import org.quickperf.sql.annotation.SqlAnnotationBuilder;

import java.lang.annotation.Annotation;
import java.util.Collection;
import java.util.Collections;

/*The configuration class has to be in org.quickperf package*/
public class QuickPerfConfiguration implements SpecifiableGlobalAnnotations {

    public Collection<Annotation> specifyAnnotationsAppliedOnEachTest() {

        // CoreAnnotationBuilder, SqlAnnotationBuilder and JvmAnnotationBuilder help to build and configure global annotations
        Annotation expectedSelectNumber = SqlAnnotationBuilder.expectSelectNumber(3);

        return Collections.singletonList(expectedSelectNumber);

    }

}
```

<p><img src="https://github.com/quick-perf/doc/blob/master/doc/images/Scopes.PNG"</p>


```java
import org.junit.Test;

public class AClassWithGlobalScopeAnnotationAppliedTest {

     //@ExpectSelectNumber(3) annotation is applied
     @Test
     public void a_test_method() {
         //...
     }

}
```

```java
package org.mycompany;

import org.junit.Test;
import org.quickperf.sql.annotation.MaxSqlSelect;

@ExpectSelectNumber(2) // CLASS SCOPE
                      // This annotation overrides the annotation
                      // defined in QuickPerfConfiguration class (GLOBAL SCOPE)
public class AClassWithAnnotationsTest {

    // @ExpectSelectNumber(2) annotation placed on class is applied
    @Test
    public void a_test_method() {
        //...
    }

    @ExpectSelectNumber(1) // METHOD SCOPE
                          // This annotation overrides the annotation placed
                          // on class
    @Test
    public void a_test_method_with_quick_perf_annotation() {
        //...
    }

}
```
