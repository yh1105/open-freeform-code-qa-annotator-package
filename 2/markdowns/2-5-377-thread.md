
# Post \#65596715 [Link](https://stackoverflow.com/questions/65596715/)

## How to format LocalDate to ISO 8601 with T and Z?

**Vote**: 5 (499/702) **Views**: 18142 (318/702) 

**Internal ID** \#2-5-377

Created at 2021-01-06 13:31:38

Tags: `java` `datetime` `datetime-format` `localdate` `datetimeformatter`

----------

#### Metadata:

Area: `Back-end`

Language: `java` (full parsed tag list: `java`)

----------

**Notepad**


----------

I'm trying to generate a random date and time, and convert it to the `"yyyy-MM-dd'T'HH:mm:ss'Z'"` format.
Here is what I have tried:
```
public static String generateRandomDateAndTimeInString() {
    LocalDate date = LocalDate.now().minus(Period.ofDays((new Random().nextInt(365 * 70))));
    System.out.println("date and time :: " + date.toString());
    return formatDate(date) ;
  }

  public static String formatDate(LocalDate date){
    DateFormat dateFormat = new SimpleDateFormat("yyyy-MM-dd'T'HH:mm:ss'Z'");
    return dateFormat.format(date);
  }
```

But in the line `dateFormat.format(date)`, it complains with:
> java.lang.IllegalArgumentException: Cannot format given Object as a Date
The second problem is that, the output of print does not contain the time:
```
date :: 1998-12-24
```

I don't know how to get it to work.


----------
        
## Answer \#0

**Accepted** Vote: 15

Created at 2021-01-06 13:51:00

------------


## Never format the java.time types using SimpleDateFormat


Using the `SimpleDateFormat`, you are supposed to format only legacy date-time types e.g. `java.util.Date`. In order to format the `java.time` date-time types, you need to use [DateTimeFormatter](https://docs.oracle.com/javase/8/docs/api/java/time/format/DateTimeFormatter.html).

## Never enclose Z within single quotes


It's a blunder to enclose `Z` within single quotes in a format. The symbol `Z` stands for `zulu` and specifies `UTC+00:00`. If you enclose it within single quotes, it will simply mean character literal, `Z` and won't function as `UTC+00:00` on parsing.

## You do not need to use a formatter explicitly


For this requirement, you do not need to use a formatter explicitly because the `OffsetDateTime#toString` already returns the string in the format that you need. However, if the number of  in an `OffsetDateTime` object is zero, the same and the subsequent smaller units are truncated by `OffsetDateTime#toString`. If you need the full format irrespective of the value of , then, of course, you will have to use `DateTimeFormatter`.
```
import java.time.LocalDate;
import java.time.Period;
import java.time.ZoneOffset;
import java.time.format.DateTimeFormatter;
import java.util.Random;

public class Main {
    public static void main(String[] args) {
        System.out.println(generateRandomDateAndTimeInString());
    }

    public static String generateRandomDateAndTimeInString() {
        LocalDate date = LocalDate.now().minus(Period.ofDays((new Random().nextInt(365 * 70))));
        System.out.println("date and time :: " + date.toString());
        return formatDate(date);
    }

    public static String formatDate(LocalDate date) {
        DateTimeFormatter dtf = DateTimeFormatter.ofPattern("uuuu-MM-dd'T'HH:mm:ssX");
        // return date.atStartOfDay().atOffset(ZoneOffset.UTC).toString();
        return date.atStartOfDay().atOffset(ZoneOffset.UTC).format(dtf);
    }
}
```


```
date and time :: 1996-09-05
1996-09-05T00:00:00Z
```

Note that the date-time API of `java.util` and their formatting API, `SimpleDateFormat` are outdated and error-prone. It is recommended to stop using them completely and switch to the [modern date-time API](https://www.oracle.com/technical-resources/articles/java/jf14-date-time.html).
- [ThreeTen-Backport](http://www.threeten.org/threetenbp/)- [Java 8+ APIs available through desugaring](https://developer.android.com/studio/write/java8-support-table)[How to use ThreeTenABP in Android Project](https://stackoverflow.com/questions/38922754/how-to-use-threetenabp-in-android-project)
Learn more about the modern date-time API from [Trail: Date Time](https://docs.oracle.com/javase/tutorial/datetime/index.html).

## If you still need to use SimpleDateFormat for whatsoever reason:


Convert `LocalDate` to `ZonedDateTime` with `ZoneOffset.UTC` and at the start of the day ➡️ Convert `ZonedDateTime` to `Instant` ➡️ Obtain `java.util.Date` object from `Instant`.
```
public static String formatDate(LocalDate date) {
    Date utilDate = Date.from(date.atStartOfDay(ZoneOffset.UTC).toInstant());
    DateFormat dateFormat = new SimpleDateFormat("yyyy-MM-dd'T'HH:mm:ssX");
    return dateFormat.format(utilDate);
}
```



------------
    
    
## Answer \#1

 Vote: 3

Created at 2021-01-06 13:40:16

------------

If you want to ignore the time part then you can use `ZonedDateTime` like this:
```
DateTimeFormatter dateFormat = DateTimeFormatter.ofPattern("yyyy-MM-dd'T'HH:mm:ssZ");
return ZonedDateTime.of(
        date, 
        LocalTime.MIN, 
        ZoneId.of("Europe/Paris")
).format(dateFormat);
```


```
2013-10-19T00:00:00+0200
```

Or much better, you can use just `toString` to get a formatted date as a String with the default format of `ZonedDateTime`:
```
return ZonedDateTime.of(
        date,
        LocalTime.MIN,
        ZoneId.of("Europe/Paris")
).toString();
```


```
2013-10-19T00:00+02:00[Europe/Paris]
```


This date are always with `00:00:00` for time part, because we are using `LocalTime.MIN`
Also, you can change the `ZoneId` to the expected `Zone`, this was just an example.

`DateFormat` and `SimpleDateFormat` are legacy library, so please don't mix them with the `java.time` library, in the top you are using `LocalDate` which mean you are using this `java.time` library so keep going with it in all your code.


------------
    
    
## Answer \#2

 Vote: 2

Created at 2021-01-07 07:32:09

------------

```
ZoneOffset utc = ZoneOffset.UTC;
    LocalDate today = LocalDate.now(utc);
    LocalDate seventyYearsAgo = today.minusYears(70);
    int totalDays = Math.toIntExact(ChronoUnit.DAYS.between(seventyYearsAgo, today));
    LocalDate date = today.minusDays(new Random().nextInt(totalDays));
    String dateString = date.atStartOfDay(utc).toString();
    System.out.println("date and time :: " + dateString);
```

Example output:
> date and time :: 1983-08-24T00:00Z
Points to note:
- - `ZonedDateTime``OffsetDateTime``LocalDate``Z`
If you want hours, minutes and seconds in the output too, you can have that by counting seconds rather than days. In this case use `OffsetDateTime` for the entire operation (or `ZonedDateTime` if in a time zone different from UTC).
```
ZoneOffset utc = ZoneOffset.UTC;
    OffsetDateTime today = OffsetDateTime.now(utc).truncatedTo(ChronoUnit.SECONDS);
    OffsetDateTime seventyYearsAgo = today.minusYears(70);
    long totalSeconds = ChronoUnit.SECONDS.between(seventyYearsAgo, today);
    OffsetDateTime date = today.minusSeconds(ThreadLocalRandom.current().nextLong(0, totalSeconds));
    String dateString = date.toString();
    System.out.println("date and time :: " + dateString);
```

> date and time :: 1996-09-21T06:49:56Z
I am using `ThreadLocalRandom` because it can generate a random long value in a specified interval. Funnily `ThreadLocalRandom` has a lot of convenient methods that `Random` hasn’t got.


------------
    
    