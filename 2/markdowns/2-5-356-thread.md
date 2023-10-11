
# Post \#72388363 [Link](https://stackoverflow.com/questions/72388363/)

## Java sort list with streams first by length then alphabetically

**Vote**: 2 (631/702) **Views**: 571 (688/702) 

**Internal ID** \#2-5-356

Created at 2022-05-26 07:38:39

Tags: `java` `sorting` `java-stream` `compare` `comparator`

----------

#### Metadata:

Area: `Back-end`

Language: `java` (full parsed tag list: `java`)

----------

**Notepad**


----------

I can sort already by the description length, but how can I alphabetically sort two `Article` if two of them have the same length? (if the description of two articles has the same length, then the sorting should be alphabetical).
```
public List<Article> sortAsc() {
    removeNull();
    return articles.stream()
            .sorted(Comparator.comparingInt(a -> a.getDescription().length()))
            .collect(Collectors.toList());
}




public class ComparatorAppController implements Comparator<String> {

/***
 * compare each element
 * @param o1
 * @param o2
 * @return
 */
public int compare(String o1, String o2) {
    // check length in one direction
    if (o1.length() > o2.length()) {
        return 1;
    }
    // check length in the other direction
    else if (o1.length() < o2.length()) {
        return -1;
    }
    // if same alphabetical order
    return o1.compareTo(o2);
}
```

}
How can I use my Comparator for this situation? Or should I change it to something else?


----------
        
## Answer \#0

**Accepted** Vote: 1

Created at 2022-05-26 07:55:31

------------

If you need to sort first by description length and then by description (alphabetical order), then your first comparison is fine but you also need to add a second comparison by description.
You can stack a second comparison by using the method `thenComparing()`. It will perform the second comparison only for elements with same length. There is no need to implement a custom `Comparator` for this scenario.
```
public List<Article> sortAsc() {
    removeNull();
    return articles.stream()
            .sorted(Comparator.comparingInt((Article a) -> a.getDescription().length())
                .thenComparing(Article::getDescription))
            .collect(Collectors.toList());
}
```



------------
    
    
## Answer \#1

 Vote: 2

Created at 2022-05-26 07:46:57

------------

Your custom comparator seems good. However in the `sorted` method of `streams` you use another comparator.
This is how you can pluggin your custom comparator considering it is in the same class with the following block of code.
```
return articles.stream()
            .sorted(this::compare)
            .collect(Collectors.toList());
```



------------
    
    
## Answer \#2

 Vote: 1

Created at 2022-05-26 07:48:31

------------

Use `Comparator.comparing(KeyExtractor,Comparator)`
```
public List<Article> sortAsc() {
    removeNull();
    return articles.stream()
            .sorted(Comparator.comparing(a -> a.getDescription(), new ComparatorAppController()))
            .collect(Collectors.toList());
}
```

Or define all the criterias with some `thenComparing*`
```
public static List<Article> sortAsc() {
    return articles.stream()
            .sorted(Comparator.<Article>comparingInt(a -> a.getDescription().length())
                    .thenComparing(Article::getDescription))
            .collect(Collectors.toList());
}
```



------------
    
    
## Answer \#3

 Vote: 1

Created at 2022-05-26 07:56:00

------------

Why you need to sort in `stream` ,you can simply call 'sort' method for your list.
```
articles.sort(new ComparatorAppController());
```

You can add n numbers of comparator in `sort` method.
eg:-
```
articles.sort(new ComparatorAppController().thenComparing(new SomeOtherComparing()));
```

You can also use `thenComparing` in the `stream` also.
```
articles.stream()
            .sorted(Comparator.comparingInt(a -> a.getDescription().length()).thenComparing(new SomeOtherComparing()))
            .collect(Collectors.toList());
```



------------
    
    
## Answer \#4

 Vote: 1

Created at 2022-05-26 07:57:16

------------

Delete your comparator and build one using `comparing()` and `thenComparing()`:
```
articles.stream()
  .sorted(Comparator.comparing(a -> a.getDescription().length())
                    .thenComparing(Article::getDescription))
        .collect(Collectors.toList());
```



------------
    
    