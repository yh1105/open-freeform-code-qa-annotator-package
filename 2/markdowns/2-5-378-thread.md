
# Post \#60081577 [Link](https://stackoverflow.com/questions/60081577/)

## What is best way of reading test data from CSV file with JUnit 5?

**Vote**: 3 (575/702) **Views**: 11848 (396/702) 

**Internal ID** \#2-5-378

Created at 2020-02-05 18:00:14

Tags: `java` `unit-testing` `csv` `junit` `junit5`

----------

#### Metadata:

Area: `Back-end`

Language: `java` (full parsed tag list: `csv` `java`)

----------

**Notepad**


----------

What is best/suggested way of reading test data from CSV file with JUnit 5?

Assuming one line is one test case, one column is one parameter, number of columns is fixed and different columns can have different (basic) types: String, int, double. 

An example would be:

```
public class Test{

    @Test
    // ready to use annotation that may load parameters from each line 
    public void shouldCalculateDiscount(String column1, long column2, double column3) {
        assertEquals(5, column1.length());
        assertEquals(column3, column2, 0.0001);
    }
}
```


CSV file example:

```
column1,column2,column3
a,0,0.0
abcde,1,1.01
a-b-c,999,999.0
```


Approach that uses annotations would be perfect, probably something analogical to JUnit's @ParameterizedTest.


----------
        
## Answer \#0

**Accepted** Vote: 6

Created at 2020-02-05 21:09:42

------------

Suprisingly I have found two well documented approaches that really answer my question.

 one is from book  by Tomek Kaczanowski.
He suggests to use the following:

```
@RunWith(JUnitParamsRunner.class)
public class ReadCSVJunitParamsTest {

    @Test
    @FileParameters(value = "classpath:financial.csv", mapper = CsvWithHeaderMapper.class)
    public void testLetterCount(String value, long letterCount) {
        assertEquals(letterCount, LetterCounter.countLetters(value));
    }
}
```


It uses [JUnitParams](https://github.com/Pragmatists/JUnitParams) library. It can even test a n-fold cartesian product of parameter values "" - see `@CombinedParameters` annotation.

 approach uses combination of [@CsvSource](https://junit.org/junit5/docs/current/user-guide/#writing-tests-parameterized-tests-sources-CsvFileSource) and @ParameterizedTest, straight from.. JUnit 5.

```
public class ReadCSVJUnitParametrized {

    @ParameterizedTest
    @CsvFileSource(resources = "/financial.csv", numLinesToSkip = 1)
    public void testLetterCountParametrized(String value, long letterCount) {
        assertEquals(letterCount, LetterCounter.countLetters(value));
    }
}
```


The  file I used for testing:

```
value,letterCount
,0
a,1
..aa,2
.!@#$%^&*(,0
0123456789,0
abcdefghij0,10
```


 class:

```
public class LetterCounter {
    public static long countLetters(String value) {
        if (value == null) {
            return 0;
        }
        return value.chars().filter(Character::isLetter).count();
    }
}
```



------------
    
    
## Answer \#1

 Vote: 2

Created at 2020-02-05 20:06:00

------------

CSV stands for comma separated values, so inherently you can just split since you know the delimiter used. If you are trying to use the test data for multiple tests, you could try:

```
List<String[]> columnValues = new ArrayList<String[]>();

@Before
public void init(){
    try {
        Scanner scanner = new Scanner(new File("myFile.csv"));
        while(scanner.hasNextLine()) {
            String[] values = scanner.nextLine().split(",");
            columnValues.add(values);
        }
    }catch(Exception e) {
        //whatever
    }
}
```


Then in whatever test method you are using, you can simply break down the values

```
@Test
public void myTest(){
    //set x to 1 to avoid columns
    for(int x = 1; x < columnValues.size(); x++) {
        String col1 = columnValues.get(x)[0];
        int col2 = Integer.parseInteger(columnValues.get(x)[1]);
        double col3 = Double.parseDouble(columnValues.get(x)[2]);
        double col4 = Double.parseDouble(columnValues.get(x)[3]);
        //test values here
    }
}
```


or alternatively use the specific indexes if you want specific lines tested, or even randomize it if you felt like it rather than testing all lines. You could also, as you have mentioned, use this concept similarly as parameters for tests.


------------
    
    
## Answer \#2

 Vote: 1

Created at 2020-02-05 18:32:20

------------

Apache commons has a good CSV library that you can use. 
 [http://commons.apache.org/proper/commons-csv/user-guide.html](http://commons.apache.org/proper/commons-csv/user-guide.html)


------------
    
    