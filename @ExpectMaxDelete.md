With this annotation, the test will fail if the number of DELETE statements is greater than expected. 

See also the [ExpectDelete](./@ExpectDelete) annotation.

### :wrench: Elements 
|Name      |Type | Meaning                   | Default value  |
| -------- |:---:|:-------------------------:|:--------------:|
| value    | int |Maximum number of deletes  |        0       |

### :mag_right: Example
```java
    @ExpectMaxDelete(1)
    @Test
    public void execute_one_delete() {	
     //...
    }
```