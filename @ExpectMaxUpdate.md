With this annotation, the test will fail if the number of UPDATE statements is greater than expected. 

See also the [ExpectUpdate](./@ExpectUpdate) annotation.

### :wrench: Elements 
|Name      |Type | Meaning                   | Default value  |
| -------- |:---:|:-------------------------:|:--------------:|
| value    | int |Maximum number of updates  |        0       |

### :mag_right: Example
    @ExpectMaxUpdate(1)