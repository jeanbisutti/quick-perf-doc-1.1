With this annotation, the test will fail if the number of INSERT statements is greater than expected. 

See also the [ExpectInsert](./@ExpectInsert) annotation.

### :wrench: Elements 
|Name      |Type | Meaning                   | Default value  |
| -------- |:---:|:-------------------------:|:--------------:|
| value    | int |Maximum number of inserts  |        0       |

### :mag_right: Example
```java
    @ExpectMaxInsert(1)
    @Test
    public void execute_one_insert() {	
     //...
    }
```