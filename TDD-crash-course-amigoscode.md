## Test Driven Development [Amigoscode](https://www.youtube.com/watch?v=z6gOPonp2t0)

Benefits:
- less time debugging the code because your code will be solid
- reducing cost, and more



In this course:
![Diagram](https://github.com/20b2122/TDD/blob/main/TDD-crash-course-amigoscode.jpg)

* Coding for a subset of the payment unit and how to perform test driven develoment



Cloning Repository [github](https://github.com/amigoscode/software-testing)
- [6-integration-testing](https://github.com/amigoscode/software-testing/tree/6-integration-testing)
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

@Service
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



### Parameterized Tests

- chagne the void itShouldValidatePhoneNumber in **PhoneNumberValidatorTest** into this
- remove all tests **@Test**

``` Java

@ParameterizedTest
@CsvSource({"+447000000000", true", "447000000000", false", "+4470000000008878", false" })
void itShouldValidatePhoneNumber(String input, boolean expected) {
        // When
        boolean isValid = underTest.test(phoneNumber);
        
        // Then
        assertThat(isValid).isEqualTo(expected);
}

```




---



### Using Phone Number Validator and Running All Tests
- add phone number validator class inside of the **CustomerRegistrationService** class

``` Java

package com.amigoscode.testing.customer;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.Optional;
import java.util.UUID;

@Service
public class CustomerRegistrationService {

    private final CustomerRepository customerRepository;
    private final PhoneNumberValidator phoneNumberValidator;

    @Autowired
    public CustomerRegistrationService(CustomerRepository customerRepository, 
                                       PhoneNumberValidator phoneNumberValidator) {
        this.customerRepository = customerRepository;
        this.phoneNumberValidator = phoneNumberValidator;
    }

    public void registerNewCustomer(CustomerRegistrationRequest request) {
        String phoneNumber = request.getCustomer().getPhoneNumber();
        
        // To do : Validate that phone number is valid
        if (phoneNumberValidator.test(phoneNumber)) {
            throw new IllegalStateException("Phone Number " + phneNumber + " is not valid");
        }
        
        Optional<Customer> customerOptional = customerRepository
                .selectCustomerByPhoneNumber(phoneNumber);

        if (customerOptional.isPresent()) {
            Customer customer = customerOptional.get();
            if (customer.getName().equals(request.getCustomer().getName())) {
                return;
            }
            throw new IllegalStateException(String.format("phone number [%s] is taken", phoneNumber));
        }

        if(request.getCustomer().getId() == null) {
            request.getCustomer().setId(UUID.randomUUID());
        }

        customerRepository.save(request.getCustomer());
    }
}

```

- add code into **CustomerRegistrationServiceTest**

``` Java

package com.amigoscode.testing.customer;

import com.amigoscode.testing.utils.PhoneNumberValidator;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.ArgumentCaptor;
import org.mockito.Captor;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;

import java.util.Optional;
import java.util.UUID;

import static org.assertj.core.api.Assertions.assertThat;
import static org.assertj.core.api.Assertions.assertThatThrownBy;
import static org.mockito.ArgumentMatchers.any;
import static org.mockito.BDDMockito.given;
import static org.mockito.BDDMockito.then;
import static org.mockito.Mockito.never;

class CustomerRegistrationServiceTest {

    @Mock
    private CustomerRepository customerRepository;

    @Mock
    private PhoneNumberValidator phoneNumberValidator;

    @Captor
    private ArgumentCaptor<Customer> customerArgumentCaptor;

    private CustomerRegistrationService underTest;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.initMocks(this);
        underTest = new CustomerRegistrationService(customerRepository, phoneNumberValidator);
    }

    @Test
    void itShouldSaveNewCustomer() {
        // Given a phone number and a customer
        String phoneNumber = "000099";
        Customer customer = new Customer(UUID.randomUUID(), "Maryam", phoneNumber);

        // ... a request
        CustomerRegistrationRequest request = new CustomerRegistrationRequest(customer);

        // ... No customer with phone number passed
        given(customerRepository.selectCustomerByPhoneNumber(phoneNumber))
                .willReturn(Optional.empty());

        //... Valid phone number
        given(phoneNumberValidator.test(phoneNumber)).willReturn(true);

        // When
        underTest.registerNewCustomer(request);

        // Then
        then(customerRepository).should().save(customerArgumentCaptor.capture());
        Customer customerArgumentCaptorValue = customerArgumentCaptor.getValue();
        assertThat(customerArgumentCaptorValue).isEqualTo(customer);
    }

    @Test
    void itShouldNotSaveNewCustomerWhenPhoneNumberIsInvalid() {
        // Given a phone number and a customer
        String phoneNumber = "000099";
        Customer customer = new Customer(UUID.randomUUID(), "Maryam", phoneNumber);

        // ... a request
        CustomerRegistrationRequest request = new CustomerRegistrationRequest(customer);


        //... Valid phone number
        given(phoneNumberValidator.test(phoneNumber)).willReturn(false);

        // When
        assertThatThrownBy(() -> underTest.registerNewCustomer(request))
                .isInstanceOf(IllegalStateException.class)
                .hasMessageContaining("Phone Number " + phoneNumber + " is not valid");

        // Then
        then(customerRepository).shouldHaveNoInteractions();
    }

    @Test
    void itShouldSaveNewCustomerWhenIdIsNull() {
        // Given a phone number and a customer
        String phoneNumber = "000099";
        Customer customer = new Customer(null, "Maryam", phoneNumber);

        // ... a request
        CustomerRegistrationRequest request = new CustomerRegistrationRequest(customer);

        // ... No customer with phone number passed
        given(customerRepository.selectCustomerByPhoneNumber(phoneNumber))
                .willReturn(Optional.empty());

        //... Valid phone number
        given(phoneNumberValidator.test(phoneNumber)).willReturn(true);

        // When
        underTest.registerNewCustomer(request);

        // Then
        then(customerRepository).should().save(customerArgumentCaptor.capture());
        Customer customerArgumentCaptorValue = customerArgumentCaptor.getValue();
        assertThat(customerArgumentCaptorValue)
                .isEqualToIgnoringGivenFields(customer, "id");
        assertThat(customerArgumentCaptorValue.getId()).isNotNull();
    }

    @Test
    void itShouldNotSaveCustomerWhenCustomerExists() {
        // Given a phone number and a customer
        String phoneNumber = "000099";
        Customer customer = new Customer(UUID.randomUUID(), "Maryam", phoneNumber);

        // ... a request
        CustomerRegistrationRequest request = new CustomerRegistrationRequest(customer);

        // ... an existing customer is retuned
        given(customerRepository.selectCustomerByPhoneNumber(phoneNumber))
                .willReturn(Optional.of(customer));

        //... Valid phone number
        given(phoneNumberValidator.test(phoneNumber)).willReturn(true);

        // When
        underTest.registerNewCustomer(request);

        // Then
        then(customerRepository).should(never()).save(any());
    }

    @Test
    void itShouldThrowWhenPhoneNumberIsTaken() {
        // Given a phone number and a customer
        String phoneNumber = "000099";
        Customer customer = new Customer(UUID.randomUUID(), "Maryam", phoneNumber);
        Customer customerTwo = new Customer(UUID.randomUUID(), "John", phoneNumber);

        // ... a request
        CustomerRegistrationRequest request = new CustomerRegistrationRequest(customer);

        // ... No customer with phone number passed
        given(customerRepository.selectCustomerByPhoneNumber(phoneNumber))
                .willReturn(Optional.of(customerTwo));

        //... Valid phone number
        given(phoneNumberValidator.test(phoneNumber)).willReturn(true);

        // When
        // Then
        assertThatThrownBy(() -> underTest.registerNewCustomer(request))
                .isInstanceOf(IllegalStateException.class)
                .hasMessageContaining(String.format("phone number [%s] is taken", phoneNumber));

        // Finally
        then(customerRepository).should(never()).save(any(Customer.class));

    }
}

```
