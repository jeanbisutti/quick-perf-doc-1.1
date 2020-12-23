Verifies the number of SELECT statements.

See also the [ExpectMaxSelect](./@ExpectMaxSelect) annotation.

### :wrench: Elements 
|Name      |Type | Meaning                   | Default value  |
| -------- |:---:|:-------------------------:|:--------------:|
| value    | int |Number of select statements|       1        |

### :mag_right: Example
```java
    @ExpectSelect(1)
    @Test
    public void should_retrieve_all_cars() {	
     //...
    }
```