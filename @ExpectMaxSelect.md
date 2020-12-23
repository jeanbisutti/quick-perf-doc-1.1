With this annotation, the test will fail if the number of SELECT statements is greater than expected. 
### :wrench: Elements 
|Name      |Type | Meaning                   | Default value  |
| -------- |:---:|:-------------------------:|:--------------:|
| value    | int |Maximum number of selects  |        0       |

### :mag_right: Example
```java
    @ExpectMaxSelect(1)
    @Test
    public void should_retrieve_all_cars() {	
     //...
    }
```