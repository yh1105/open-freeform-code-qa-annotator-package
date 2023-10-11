
# Post \#61702224 [Link](https://stackoverflow.com/questions/61702224/)

## How to use pair in a priority queue and then return the value using the key as the priority

**Vote**: 9 (409/702) **Views**: 15682 (347/702) 

**Internal ID** \#2-5-336

Created at 2020-05-09 19:03:33

Tags: `java` `collections` `key-value` `priority-queue`

----------

#### Metadata:

Area: `Back-end`

Language: `java` (full parsed tag list: `java`)

----------

**Notepad**


----------

So I want to use the smallest key as the priority and then return the VALUE of that corresponding key:

```
import javafx.util.Pair;
import java.util.PriorityQueue;

public class Test
{
    public static void main (String[] args)
    {
        int n = 5;

        PriorityQueue <Pair <Integer,Integer> > l = new PriorityQueue <Pair <Integer,Integer> > (n);

        l.add(new Pair <> (1, 90));
        l.add(new Pair <> (7, 54));
        l.add(new Pair <> (2, 99));
        l.add(new Pair <> (4, 88));
        l.add(new Pair <> (9, 89));

        System.out.println(l.poll().getValue()); 
    }
}
```


The output Im looking for is 90 because 1 is the smallest key. Its fine even if the value is used as the priority and the key is returned because I can just swap the data if neccessary. I want to display key/value using value/key as a priority (minimum value in this case). I do not know how this can be done in this scenario. This works fine in C++.


----------
        
## Answer \#0

**Accepted** Vote: 14

Created at 2020-05-09 19:21:02

------------

You need to use `Comparator` which will be used to order this priority queue.

Use `Comparator.comparing()` and pass `Method reference` of  comparing parameter when create the `PriorityQueue`

```
PriorityQueue<Pair<Integer,Integer> > pq=
                new PriorityQueue<Pair<Integer,Integer>>(n, Comparator.comparing(Pair::getKey));
```


Or 

You can use lambda expression 

```
PriorityQueue<Pair<Integer,Integer> > pq=
                    new PriorityQueue<Pair<Integer,Integer>>(n,(a,b) -> a.getKey() - b.getKey());
```



------------
    
    
## Answer \#1

 Vote: 7

Created at 2020-05-13 19:50:16

------------

Alternative, use custom `Pair` class that implements `Comparable`.

```
class Pair implements Comparable<Pair> {
    Integer value;
    Integer index;

    public Pair(Integer value, Integer index) {
        this.value = value;
        this.index = index;
    }

    @Override
    public int compareTo(Pair o) {
        return value - o.value;
    }
}
```


and then usage

```
// Add elements
queue.add(new Pair(valueKey, index));
// do it for all your elements

final Pair min = queue.poll();
Integer index = min.index;
Integer value = min.value;

// Do with them what you want.
```


I used PriorityQueue in leetcode challenge.
[https://leetcode.com/problems/merge-k-sorted-lists/discuss/630580/Using-PriorityQueue-in-Java](https://leetcode.com/problems/merge-k-sorted-lists/discuss/630580/Using-PriorityQueue-in-Java)

And this is full example
[https://github.com/yan-khonski-it/leetcode/blob/master/src/main/java/com/yk/training/leetcode/merge_sorted_lists/PriorityQueueSolution.java](https://github.com/yan-khonski-it/leetcode/blob/master/src/main/java/com/yk/training/leetcode/merge_sorted_lists/PriorityQueueSolution.java)


------------
    
    
## Answer \#2

 Vote: 1

Created at 2021-07-21 05:17:04

------------

I prefer to use Map.Entry in most cases for this purpose. Other time a custom Pair class with Comparator in PriorityQueue constructor.
```
import java.util.PriorityQueue;
import java.util.Map;
import java.util.HashMap;

public class Test
{
    public static void main (String[] args)
    {
        int n = 5;

        PriorityQueue <Map.Entry<Integer,Integer>> l = new PriorityQueue <> (n,(a,b)->Integer.compare(a.getKey(),b.getKey()));
        Map<Integer,Integer> map = new HashMap<>();
        map.put(1,90);
        map.put(7,54);
        map.put(2,99);
        map.put(4,88);
        map.put(9,89);
        l.addAll(map.entrySet());
        System.out.println(l.poll().getValue()); 
    }
}
```



------------
    
    