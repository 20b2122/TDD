## Test Driven Development [Amigoscode](https://www.youtube.com/watch?v=z6gOPonp2t0)

Benefits:
- less time debugging the code because your code will be solid
- reducing cost, and more



In this course:
![Diagram](https://github.com/20b2122/TDD/blob/main/TDD-crash-course-amigoscode.jpg)

* Coding for a subset of the payment unit and how to perform test driven develoment



Cloning Repository [github](https://github.com/amigoscode/software-testing)
- can use command prompt or download zip.



---



### Lets starts with an empty test class
1. create a new package inside the test folder, name it **utils**
2. create class inside of utils, **PhoneNumberValidatorTest**



---



### Lets create the class under underTest

``` java

package com.amigoscode.testing.utils;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.CsvSource;

import static org.assertj.core.api.Assertions.assertThat;

class PhoneNumberValidatorTest {

    // create a class name PhoneNumberValidator
    private PhoneNumberValidator underTest;
    
    @BeforeEach
    void setUp() {
        underTest = new PhoneNumberValidator();
    }
    
    @Test
    void itShouldValidatePhoneNumber(String phoneNumber, boolean expected) {
        // Given
        // When
        // Then
    }

}

```



---



### Bare Bones of Class Under Test

``` Java

class PhoneNumberValidatorTest {

    private PhoneNumberValidator underTest;
    
    @BeforeEach
    void setUp() {
        underTest = new PhoneNumberValidator();
    }
    
    @Test
    void itShouldValidatePhoneNumber(String phoneNumber, boolean expected) {
        // Given
        String phoneNumber = "+447000000000";
        
        // When
        boolean isValid = underTest.test(phoneNumber);
        
        // Then
        assertThat(isValid).isTrue();
    }

}

```

``` Java

public class PhoneNumberValidator implements Predicate<String> {

    @Override
    public boolean test(String s) {
      return false; // this will cause a fail when running the code
    }

}

```



---



### Lets Impliment The Business Logic

``` Java

public class PhoneNumberValidator implements Predicate<String> {

    @Override
    public boolean test(String phoneNumber) {
      phoneNumber.startsWith("+44")) && phoneNumber.legnth == 13;
    }

}

```

``` Java

class PhoneNumberValidatorTest {

    private PhoneNumberValidator underTest;
    
    @BeforeEach
    void setUp() {
        underTest = new PhoneNumberValidator();
    }
    
    @Test
    void itShouldValidatePhoneNumber(String phoneNumber, boolean expected) {
        // Given
        String phoneNumber = "+447000000000";
        
        // When
        boolean isValid = underTest.test(phoneNumber);
        
        // Then
        assertThat(isValid).isTrue();
    }
    
    @Test
    @DisplayName("should fail when length is bigger than 13")
    void itShouldValidatePhoneNumberWhenIncorrectAndHasLengthBiggerThan13() {
        // Given
        String phoneNumber = "+4470000000008878";
        
        // When
        boolean isValid = underTest.test(phoneNumber);
        
        // Then
        assertThat(isValid).isFalse();
    }
    
    @Test
    @DisplayName("should fail when does not start with +")
    void itShouldValidatePhoneNumberWhenDoesNotStartWithPlustSign() {
        // Given
        String phoneNumber = "447000000000";
        
        // When
        boolean isValid = underTest.test(phoneNumber);
        
        // Then
        assertThat(isValid).isFalse();
    }

}

```




---



### Parametrized Tests

``` Java



```




``` Java



```




``` Java



```




``` Java



```
