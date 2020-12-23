With this annotation, the test will fail if the number of returned columns is greater than expected.

💡 **_[Why limit the number of selected columns?](Why-limit-the-number-of-selected-columns)_**

### :wrench: Elements 
|Name       |Type | Meaning                             | Default value  |
| --------  |:---:|:----------------------------------:|:--------------:|
| value     | int |Maximum number of returned columns  |        0       |
### :mag_right: Example
```java
    @ExpectMaxSelectedColumn(5)
```