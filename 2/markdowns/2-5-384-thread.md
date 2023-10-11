
# Post \#63849245 [Link](https://stackoverflow.com/questions/63849245/)

## How to mock class with @ConfigurationProperties in Spring Boot

**Vote**: 3 (575/702) **Views**: 6679 (489/702) 

**Internal ID** \#2-5-384

Created at 2020-09-11 14:39:39

Tags: `java` `spring-boot` `mocking` `mockito` `springmockito`

----------

#### Metadata:

Area: `Back-end`

Language: `java` (full parsed tag list: `java`)

----------

**Notepad**


----------

I have a class that Autowires another class with @ConfigurationProperties.

### Class with @ConfigurationProperties


```
@ConfigurationProperties(prefix = "report")
public class SomeProperties {
    private String property1;
    private String property2;
...
```


### Class that Autowires above class SomeProperties


```
@Service
@Transactional
public class SomeService {
    ....
    @Autowired
    private SomeProperties someProperties;
    .... // There are other things
```

Now, I want to test  class and in my test class when I mock  class, I am getting `null` value for all the properties.

### Test class


```
@RunWith(SpringRunner.class)
@SpringBootTest(classes = SomeProperties.class)
@ActiveProfiles("test")
@EnableConfigurationProperties
public class SomeServiceTest {
    @InjectMocks
    private SomeService someService;

    @Mock // I tried @MockBean as well, it did not work
    private SomeProperties someProperties;
```

How can I mock  having properties from `application-test.properties` file.


----------
        
## Answer \#0

**Accepted** Vote: 3

Created at 2020-09-11 20:27:27

------------

You are not mocking SomeProperties if you intend to bind values from a properties file, in which case an actual instance of SomeProperties would be provided.
Mock:
```
@RunWith(MockitoJUnitRunner.class)
public class SomeServiceTest {

    @InjectMocks
    private SomeService someService;

    @Mock
    private SomeProperties someProperties;

    @Test
    public void foo() {
        // you need to provide a return behavior whenever someProperties methods/props are invoked in someService
        when(someProperties.getProperty1()).thenReturn(...)
    }
```

No Mock (`someProperties` is an real object that binds its values from some propertysource):
```
@RunWith(SpringRunner.class)
@EnableConfigurationProperties(SomeConfig.class)
@TestPropertySource("classpath:application-test.properties")
public class SomeServiceTest {
   
    private SomeService someService;

    @Autowired
    private SomeProperties someProperties;

    @Before
    public void setup() {
        someService = new someService(someProperties); // Constructor Injection
    }
    ...
```



------------
    
    
## Answer \#1

 Vote: 2

Created at 2020-09-11 17:11:45

------------

You need to stub all the property values in @Test/@Before methods of SomeServiceTest.java like :
```
ReflectionTestUtils.setField(someProperties, "property1", "value1");

ReflectionTestUtils.setField(object, name, value);
```

You can also mock the reponse for dependent classes via Mockito.when()


------------
    
    
## Answer \#2

 Vote: 1

Created at 2020-09-11 14:54:53

------------

If you are using @Mock , you need to stub the values as well. By default the value will be null for all the properties.


------------
    
    