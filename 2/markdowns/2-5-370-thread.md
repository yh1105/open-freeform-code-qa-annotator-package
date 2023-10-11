
# Post \#71463016 [Link](https://stackoverflow.com/questions/71463016/)

## Find occurrence count of the longest common Prefix/Suffix in a List of Strings?

**Vote**: 9 (409/702) **Views**: 1974 (608/702) 

**Internal ID** \#2-5-370

Created at 2022-03-14 04:32:42

Tags: `java` `string` `algorithm` `hashmap`

----------

#### Metadata:

Area: `Back-end`

Language: `java` (full parsed tag list: `java`)

----------

**Notepad**


----------

Given a list of Strings:
```
ArrayList<String> strList = new ArrayList<String>();
strList.add("Mary had a little lamb named Willy");
strList.add("Mary had a little ham");
strList.add("Old McDonald had a farm named Willy");
strList.add("Willy had a little dog named ham");
strList.add("(abc)");
strList.add("(xyz)");
strList.add("Visit Target Store");
strList.add("Visit Walmart Store");
```

This should produce the output in the form of a `HashMap<String, Integer>` `prefixMap` and `suffixMap`:
:
```
Mary had a -> 2
Mary had a little -> 2
( -> 2
Visit -> 2
```

:
```
named Willy -> 2
ham -> 2
) -> 2
Store -> 2
```

So far I'm able to generate a prefix that is present in all items in list using the following code:
```
public static final int INDEX_NOT_FOUND = -1;

public static String getAllCommonPrefixesInList(final String... strs) {
    if (strs == null || strs.length == 0) {
        return EMPTY_STRING;
    }
    
    
    final int smallestIndexOfDiff = getIndexOfDifference(strs);
    if (smallestIndexOfDiff == INDEX_NOT_FOUND) {
        
        // All Strings are identical
        if (strs[0] == null) {
            return EMPTY_STRING;
        }
        return strs[0];
    } else if (smallestIndexOfDiff == 0) {
        
        
        // No common initial characters found, return empty String
        return EMPTY_STRING;
    } else {
        
        // Common initial character sequence found, return sequence
        return strs[0].substring(0, smallestIndexOfDiff);
    }
}






public static int getIndexOfDifference(final CharSequence... charSequence) {
    if (charSequence == null || charSequence.length <= 1) {
        return INDEX_NOT_FOUND;
    }
    boolean isAnyStringNull = false;
    boolean areAllStringsNull = true;
    
    
    final int arrayLen = charSequence.length;
    int shortestStrLen = Integer.MAX_VALUE;
    int longestStrLen = 0;

    // Find the min and max string lengths - avoids having to check that we are not exceeding the length of the string each time through the bottom loop.
    for (int i = 0; i < arrayLen; i++) {
        if (charSequence[i] == null) {
            isAnyStringNull = true;
            shortestStrLen = 0;
        } else {
            areAllStringsNull = false;
            shortestStrLen = Math.min(charSequence[i].length(), shortestStrLen);
            longestStrLen = Math.max(charSequence[i].length(), longestStrLen);
        }
    }

    // Deals with lists containing all nulls or all empty strings
    
    if (areAllStringsNull || longestStrLen == 0 && !isAnyStringNull) {
        return INDEX_NOT_FOUND;
    }

    // Handle lists containing some nulls or some empty strings
    if (shortestStrLen == 0) {
        return 0;
    }

    // Find the position with the first difference across all strings
    int firstDiff = -1;
    for (int stringPos = 0; stringPos < shortestStrLen; stringPos++) {
        final char comparisonChar = charSequence[0].charAt(stringPos);
        for (int arrayPos = 1; arrayPos < arrayLen; arrayPos++) {
            if (charSequence[arrayPos].charAt(stringPos) != comparisonChar) {
                firstDiff = stringPos;
                break;
            }
        }
        if (firstDiff != -1) {
            break;
        }
    }

    if (firstDiff == -1 && shortestStrLen != longestStrLen) {
        
        // We compared all of the characters up to the length of the
        // shortest string and didn't find a match, but the string lengths
        // vary, so return the length of the shortest string.
        return shortestStrLen;
    }
    return firstDiff;
}
```

However, my goal is to include any / with at least `2+` occurrences into the resulting map.
How can this be achieved with `Java`?


----------
        
## Answer \#0

**Accepted** Vote: 4

Created at 2022-03-17 06:22:10

------------

In my understanding of this problem the most suitable data structure for solving it is an acyclic disjointed .
In general case, a  will be comprised of several unconnected . Each  will have a  structure, in the edge case it'll form a .
Basically, the most simple  approach on how to solve this problem is to create a bunch of  based on each line, and iterate over them. The drawbacks are:  (greater memory consumption), greater  (more operations required) and it's more  because more manual actions are needed.

### The description of the Graph


So I'll stick with the  as the data structure for this problem and try to keep things as simple as possible.
Let's consider the following input:
```
"Mary had a little lamb named Willy"
"Mary had a little ham"
"A B C"
```

The graphical representation of the graph will look like this;
[](https://i.stack.imgur.com/nWiy5.jpg)
The two first lines will constitute a  formed from a  (the head part) and a  (the tail part). The second cluster will be represented by a , its vertices aren't connected with vertices formed from other strings.
It's not the only way the vertexes can be structured, the head could spawn an  and a linked list could be observed somewhere in the middle.
The main takeaway is that in order to solve the problem, we need to track the chain of vertexes through all the branches until the . In these parts of the , every prefix-strings and suffix-string that is common among two or more lines will be represented by a  ().
To maintain the number of strings that are mapped to a particular , each vertex should have a variable (`int groupCount` ), which is assigned with a default value of `1` when a  is being created and incremented each time a new string gets mapped to this vertex.
Each  contains a map that holds references to its . When a new  is being added, either new `Vertex` in being created based on the given string or the  of an existing  gets incremented.
In order to conform to this task,  should maintain references to all  and . For simplicity, instead of maintaining two groups of references to adjacent nodes, and two separate count variables () in each vertex, in this solution  is actually comprised of  ( and ). And for that reason, the implementing class in named `MultiGraph`.
In order to populate both  and  with , method `addCluster()` iterates over the string of the given line by the means of `Iterator` in normal or reversed order, depending on which  is being populated.

### Depth first search


The next step after the graphs are populated is to generate the maps of strings with the frequency of `2` and greater.
For that, the classical [depth first search](https://en.wikipedia.org/wiki/Depth-first_search) algorithm is being used.
In order to implement the DFS, a mutable container that will be used as a  is required (`ArrayDeque` is being used for that purpose). The first element that is taken from the  will be placed on the top of the  and an instance of `StringBuilder` holding the name of this element will be placed in the map.
Then, to restore a string with a particular ,  will be popped from the top of the  and their  with count `> 1` in turn will be placed on top of the stack. A copy of the  with the delimiter and the neighbour's name appended will get mapped to the .
If a  changes, that indicates that the  represents the longest common string between at least two lines. In this case, prefix and count are being added to the resulting map.

### Implementation


The following implementation consists of two classes that are  and . The `MultiGraph` class acts exclusively as data structure, maintaining two graphs. The pluming code, like splitting the lines of strings, is extracted into a separate class `GraphManager`.

```
public class MultiGraph {
    private final Map<String, Vertex> heads = new HashMap<>();
    private final Map<String, Vertex> tails = new HashMap<>();

    public void addCluster(Deque<String> names) {
        addCluster(heads, names.iterator());
        addCluster(tails, names.descendingIterator());
    }

    private void addCluster(Map<String, Vertex> clusters, Iterator<String> names) {
        String rootName = names.next();
        if (clusters.containsKey(rootName)) {
            clusters.get(rootName).incrementGroupCount();
        } else {
            clusters.put(rootName, new Vertex(rootName));
        }

        Vertex current = clusters.get(rootName);
        while (names.hasNext()) {
            current = current.addNext(names.next());
        }
    }

    public Map<String, Integer> generatePrefixMap(String delimiter) {
        Map<String, Integer> countByPrefix = new HashMap<>();

        for (Vertex next: heads.values()) {
            if (next.getGroupCount() == 1) {
                continue;
            }
            performDFS(heads, countByPrefix, delimiter, next);
        }
        return countByPrefix;
    }

    public Map<String, Integer> generateSuffixMap(String delimiter) {
        Map<String, Integer> countBySuffix = new HashMap<>();

        for (Vertex next: tails.values()) {
            if (next.getGroupCount() == 1) {
                continue;
            }
            performDFS(tails, countBySuffix, delimiter, next);
        }
        return countBySuffix;
    }
    // implementation of the Depth First Search algorithm
    public void performDFS(Map<String, Vertex> clusters,
                           Map<String, Integer> countByPrefix,
                           String delimiter, Vertex next) {

        StringBuilder prefix = null;
        Vertex current = next;
        int count = next.getGroupCount();

        Deque<Vertex> stack = new ArrayDeque<>(); // create as stack
        Map<Vertex, StringBuilder> prefixByVert = new HashMap<>();
        stack.push(next); // place the first element on the stack
        prefixByVert.put(current, new StringBuilder(current.getName()));

        while (!stack.isEmpty()) {
            current = stack.pop();
            if (current.getGroupCount() < count) { // the number of strings mapped to the current Vertex has been changed
                countByPrefix.put(prefix.toString(), count); // saving the result
                count = current.getGroupCount();
            }
            prefix = prefixByVert.get(current);

            for (Vertex neighbour: current.getNextVertByVal().values()) {
                if (next.getGroupCount() == 1) {
                    continue;
                }
                stack.push(neighbour);
                prefixByVert.put(neighbour, new StringBuilder(prefix)
                                    .append(delimiter)
                                    .append(neighbour.getName()));
            }
        }

        if (prefix != null && count > 1) {
            countByPrefix.putIfAbsent(prefix.toString(), count);
        }
    }

    private static class Vertex {
        private final String name;
        private int groupCount = 1;
        private final Map<String, Vertex> nextVertByVal = new HashMap<>();

        public Vertex(String name) {
            this.name = name;
        }

        public Vertex addNext(String value) {
            if (nextVertByVal.containsKey(value)) {
                nextVertByVal.get(value).incrementGroupCount();
            } else {
                nextVertByVal.put(value, new Vertex(value));
            }
            return nextVertByVal.get(value);
        }

        public void incrementGroupCount() {
            this.groupCount++;
        }

        public String getName() {
            return name;
        }

        public int getGroupCount() {
            return groupCount;
        }

        public Map<String, Vertex> getNextVertByVal() {
            return nextVertByVal;
        }
    }
}
```

The following class deals with the task of processing the input data: it splits the lines, takes care of discarding the empty string which might take place, and packs the input into a `Deque` to accommodate the iteration in both directions in a convenient way.
It also instantiates the  and governs it's work. `GraphManager` takes care of providing the  to the graph in order to restore the initial shape of strings while the resulting maps are being created. With that you can split the given lines on a white space, by empty string to process lines character by character or by punctuation marks without changing a single line in these two classes.

```
public class GraphManager {
    private MultiGraph graph = new MultiGraph();
    private String delimiter;

    private GraphManager(String delimiter) {
        this.delimiter = delimiter;
    }

    public static GraphManager getInstance(Iterable<String> lines, String delimiter) {
        GraphManager gm = new GraphManager(delimiter);
        gm.init(lines);
        return gm;
    }

    private void init(Iterable<String> lines) {
        for (String line: lines) {
            Deque<String> names = new ArrayDeque<>();
            for (String name: line.split(delimiter)) {
                if (!name.isEmpty()) {
                    names.add(name);
                }
            }
            addCluster(names);
        }
    }

    private void addCluster(Deque<String> names) {
        graph.addCluster(names);
    }

    public Map<String, Integer> getPrefixMap() {
        return graph.generatePrefixMap(delimiter);
    }

    public Map<String, Integer> getSuffixMap() {
        return graph.generateSuffixMap(delimiter);
    }
}
```


```
public static void main(String[] args) {
    List<String> lines = List.of(
            "Mary had a little lamb named Willy", "Mary had a little ham",
            "Old McDonald had a farm named Willy", "Willy had a little dog named ham",
            "( abc )", "( xyz )", "Visit Target Store", "Visit Walmart Store");

    GraphManager gm = GraphManager.getInstance(lines, " ");
    
    System.out.println("Prefixes:");
    for (Map.Entry<String, Integer> entry: gm.getPrefixMap().entrySet()) {
        System.out.println(entry.getValue() + " " + entry.getKey());
    }

    System.out.println("\nSuffixes:");
    for (Map.Entry<String, Integer> entry: gm.getSuffixMap().entrySet()) {
        System.out.println(entry.getValue() + " " + entry.getKey());
    }
}
```


```
Prefixes:
2 Mary had a little
2 Visit
2 (

Suffixes:
2 ham
2 )
2 Store
2 Willy named
```



------------
    
    
## Answer \#1

 Vote: 4

Created at 2022-03-14 06:02:35

------------

This problem should be solved easily using a [trie](https://www.hackerearth.com/practice/data-structures/advanced-data-structures/trie-keyword-tree/tutorial/).
The trie node should basically keep a track of 2 things:

1. Child nodes
2. Count of prefixes ending at current node


Insert all strings in the trie, which will be done in `O(string length * number of strings)`. After that, simply traversing the trie, you can hash the prefixes based on the count as per your use case. For suffixes, you can use the same approach, just start traversing the strings in reverse order.
Edit:
On second thought, trie might be the most efficient way, but a simple hashmap implementation should also work here. Here's an example to generate all prefixes with count > 1.
```
import java.util.*;
import java.util.stream.*;

class Main {
  public static void main(String[] args) {
    
    System.out.println("Hello world!");

    ArrayList<String> strList = new ArrayList<String>();
    
    strList.add("Mary had a little lamb named Willy");
    strList.add("Mary had a little ham");
    strList.add("Old McDonald had a farm named Willy");
    strList.add("Willy had a little dog named ham");
    strList.add("(abc)");
    strList.add("(xyz)");
    strList.add("Visit Target Store");
    strList.add("Visit Walmart Store");

    Map<String, Integer> prefixMap = new HashMap<String, Integer>();
    ArrayList<String> stringsWithHighestOccurrence = new ArrayList<String>();

    for (String word : strList) {
            for (int i = 1; i <= word.length(); i++){
        String prefix = word.substring(0, i);
        prefixMap.merge(prefix, 1, Integer::sum);
      }
        }

    Integer maxval = Collections.max(prefixMap.values());

    for (String key: prefixMap.keySet()){
      Integer value = prefixMap.get(key);
      if (value > 1) System.out.println(key + " : " + value);
      if (value == maxval) stringsWithHighestOccurrence.add(key);
    }

    int maxLength = stringsWithHighestOccurrence.stream().map(String::length).max(Integer::compareTo).get();
    
    System.out.println(maxLength);

    ArrayList<String> prefixesWithMaxLength =
stringsWithHighestOccurrence.stream().filter(c -> c.length() == maxLength).collect(Collectors.toCollection(ArrayList::new));
    System.out.println(prefixesWithMaxLength);
  }
}
```

Nonetheless, I'll also add a basic `TrieNode` implementation for the sake of completion, since my answer proposed that approach in the first place.
`TrieNode`:
```
class TrieNode {
    private final Map<Character, TrieNode> children = new HashMap<>();
    private int count;

    Map<Character, TrieNode> getChildren() {
        return children;
    }

    boolean getCount() {
        return count;
    }

    void increaseCount() {
        this.count += 1;
    }
}
```

`Trie`:
```
class Trie {
    private TrieNode root;

    Trie() {
        root = new TrieNode();
    }

    void insert(String word) {
        TrieNode current = root;

        for (char l : word.toCharArray()) {
            current = current.getChildren().computeIfAbsent(l, c -> new TrieNode());
            current.increaseCount()
        }
    }
}
```

Traversing the trie would be analogous to a simple DFS scenario where the "path" upto current node is also maintained (we're switching the path with the prefix upto this point).


------------
    
    
## Answer \#2

 Vote: 2

Created at 2022-03-17 10:17:14

------------

To implement a [trie](https://en.wikipedia.org/wiki/Trie), first you need a node.
```
class Node<T> {

    private final T value;
    private final Node<T> parent;
    private final Map<T, Node<T>> children;
    private boolean isEnd;

    Node(T value, Node<T> parent) {
        this.value = value;
        this.parent = parent;
        this.children = new HashMap<>();
        this.isEnd = false;
    }

    Node<T> addChild(T childValue, Node<T> parent) {
        //return child node if existing, otherwise create and return
       return this.children.computeIfAbsent(childValue, value -> new Node<>(value, parent));
    }

    T getValue() {
        return this.value;
    }

    Node<T> getParent() {
        return this.parent;
    }

    boolean isEnd() {
        return this.isEnd;
    }

    void setEnd(boolean isEnd) {
        this.isEnd = isEnd;
    }

    Collection<Node<T>> children() {
        return this.children.values();
    }

    @Override
    public String toString() {
        //for easier debugging
        return "Node{" +
                "value=" + this.value +
                ", children=" + this.children.keySet() +
                ", isEnd=" + this.isEnd +
                '}';
    }
}
```

In the node we have actual value, reference to the parent node, to make it eaiser to build prefixes, child nodes mapped value to corresponding node, and a flag if node is end node.
Actual `Trie` implementation
```
public class Trie<T> {

    private final Node<T> root;

    public Trie() {
        this.root = new Node<>(null, null);
    }

    public void insert(T[] elements) {
        if (elements.length == 0) {
            //don't want to set root as end node
            return;
        }
        Node<T> currentNode = this.root;
        for (T element : elements) {
            currentNode = currentNode.addChild(element, currentNode);
        }
        currentNode.setEnd(true);
    }

    public Map<Collection<T>, Integer> countPrefixes(BiConsumer<Deque<T>, T> operation) {
        Map<Collection<T>, Integer> map = new LinkedHashMap<>();
        this.countPrefixes(this.root, map, operation);
        return map;
    }

    private void countPrefixes(Node<T> current, Map<Collection<T>, Integer> map, BiConsumer<Deque<T>, T> operation) {
        if (current != this.root) {
            //check java doc for AbstractSet hashCode and equals
            ArrayList<T> prefix = this.buildKey(current, operation);
            int childrenCount = current.children().size();
            if (childrenCount == 0 && current.isEnd()) {
                //this sets entire collection(entire sentence 'Mary had a little lamb named Willy' for example)
                //as prefix which is met once
                childrenCount = 1;
            }
            map.merge(prefix, childrenCount, Integer::sum);
            if (childrenCount > 1) {
                //each parent node is already marked as prefix met once,
                //but any node having more than one child means entire chain of nodes
                //is a prefix met the number of children,
                //so we go backwards to update parent counts with the difference
                this.updateParentPrefixes(current.getParent(), childrenCount - 1, map, operation);
            }
        }
        for (Node<T> child : current.children()) {
            //visit each child recursively to count them
            //depth first
            this.countPrefixes(child, map, operation);
        }
    }

    //operation is abstraction for the order
    //in which we want to add elements
    //when building key/prefix
    private ArrayList<T> buildKey(Node<T> node, BiConsumer<Deque<T>, T> operation) {
        Deque<T> prefix = new LinkedList<>();
        while (node.getValue() != null) {
            operation.accept(prefix, node.getValue());
            node = node.getParent();
        }
        return new ArrayList<>(prefix);
    }

    private void updateParentPrefixes(Node<T> parent, int updateCount, Map<Collection<T>, Integer> map, BiConsumer<Deque<T>, T> operation) {
        if (parent == this.root) {
            //we don't want to update root, ever!!!
            return;
        }
        ArrayList<T> prefix = this.buildKey(parent, operation);
        map.merge(prefix, updateCount, Integer::sum);
        //visit parents recursively until root
        this.updateParentPrefixes(parent.getParent(), updateCount, map, operation);
    }
}
```

It is somewhat simplified in the sense, that only `insert` is implemented, but we don't need update and delete now. Few things to note:

1. Trie must have a root node, which is empty. We are representing that with null value and null parent. We must never update this node.
2. Parameter BiConsumer<Deque<T>, T> operation of countPrefixes(). This is abstraction for the order in which we want to add elements when creating a prefix. It's needed, because counting suffixes can be represented, and is implemented, as counting prefixes when reversing the collection/sentence.
3. Return type Map<Collection<T>, Integer> of countPrefixes(). This implementation is more generic, thus each prefix is represented as collection of node values.
4. Why prefix is ArrayList? First we need to keep insertion order. Second its' implementation of hashCode() and equals() makes it good candidate for map key. To citate javadoc:


`This ensures that list1.equals(list2) implies that list1.hashCode()==list2.hashCode() for any two lists, list1 and list2, as required by the general contract of Object.hashCode.` - for hashCode.
`In other words, two lists are defined to be equal if they contain the same elements in the same order.` - for equals.

1. The comments in code should explain the rest of the specifics in implementation.


A main to test.
```
public class TrieMain {

    public static void main(String[] args) {
        String spacePattern = "\\s+";
        String emptyStringPattern = "";

        ArrayList<String> strList = new ArrayList<>();
        strList.add("Mary had a little lamb named Willy");
        strList.add("Mary had a little ham");
        strList.add("Mary had a sandwich");
        strList.add("Old McDonald had a farm named Willy");
        strList.add("Willy had a little dog named ham");
        strList.add("Willy had a big dog named Willy");
        strList.add("Willy had a huge dog named Willy");
        strList.add("(abc)");
        strList.add("(xyz)");
        strList.add("Visit Target Store");
        strList.add("Visit Walmart Store");

        Trie<String> trie = new Trie<>();
        //using another trie for suffix to avoid counting suffix as prefix and vice versa
        Trie<String> reversedTrie = new Trie<>();
        for (String string : strList) {
            String pattern = string.startsWith("(") ? emptyStringPattern : spacePattern;
            String[] words = string.split(pattern);
            trie.insert(words);

            //reverse collection to count suffixes
            Collections.reverse(Arrays.asList(words));
            reversedTrie.insert(words);
        }
        Map<Collection<String>, Integer> prefixCount = trie.countPrefixes(Deque::addFirst);
        System.out.println("Prefixes:");
        printMap(prefixCount);
        System.out.println();
        Map<Collection<String>, Integer> suffixCount = reversedTrie.countPrefixes(Deque::addLast);
        System.out.println("Suffixes:");
        printMap(suffixCount);
    }

    private static void printMap(Map<Collection<String>, Integer> map) {
        map.entrySet()
                .stream()
                .filter(entry -> entry.getValue() > 1)
                .forEach(e -> {
                    String key = String.join(" ", e.getKey());
                    String line = String.format("%s -> %d", key, e.getValue());
                    System.out.println(line);
                });
    }
}
```

Things to note here:

1. Added few more sentences in the list for extra testing.
2. I am splitting the inputs with ( into characters and not words as the rest to achieve your output, but example is somewhat confusing. For sentences prefixes are collection of words, while for those two they are characters.
3. Need second trie for suffixes - otherwise we might count prefix as suffix and vice versa.
4. Arrays.asList() returned list is backed by input array. I am taking advantage of that to reverse the input array itself for suffixes.


 While testing with characters realized [AbstractSet](https://docs.oracle.com/javase/7/docs/api/java/util/AbstractSet.html), or any set to be precise, is a bad idea for prefix, because it does not allow duplicated elements. Updated trie implementation to use `ArrayList`. I'm also adding 2 more main methods, to demonstrate the trie working with strings and with characters:

1. Strings - each node is a word, acquired by splitting strings by space


```
public class TrieWords {

    public static void main(String[] args) {
        ArrayList<String> strList = new ArrayList<>();
        strList.add("Mary had a little lamb named Willy");
        strList.add("Mary had a little ham");
        strList.add("Mary had a sandwich");
        strList.add("Old McDonald had a farm named Willy");
        strList.add("Willy had a little dog named ham");
        strList.add("Willy had a big dog named Willy");
        strList.add("Willy had a huge dog named Willy");
        strList.add("(abc)");
        strList.add("(xyz)");
        strList.add("Visit Target Store");
        strList.add("Visit Walmart Store");

        Trie<String> trie = new Trie<>();
        //using another trie for suffix to avoid counting suffix as prefix and vice versa
        Trie<String> reversedTrie = new Trie<>();
        for (String string : strList) {
            String[] words = string.split("\\s+");
            trie.insert(words);

            //reverse collection to count suffixes
            Collections.reverse(Arrays.asList(words));
            reversedTrie.insert(words);
        }
        Map<Collection<String>, Integer> prefixCount = trie.countPrefixes(Deque::addFirst);
        System.out.println("Prefixes:");
        printMap(prefixCount);
        System.out.println();
        Map<Collection<String>, Integer> suffixCount = reversedTrie.countPrefixes(Deque::addLast);
        System.out.println("Suffixes:");
        printMap(suffixCount);
    }

    private static void printMap(Map<Collection<String>, Integer> map) {
        map.entrySet()
                .stream()
                .filter(entry -> entry.getValue() > 1)
                .forEach(e -> {
                    String key = String.join(" ", e.getKey());
                    String line = String.format("%s -> %d", key, e.getValue());
                    System.out.println(line);
                });
    }
}
```


1. Characters - each node is a character from the string


```
public class TrieChars {

    public static void main(String[] args) {
        ArrayList<String> strList = new ArrayList<>();
        strList.add("Mary had a little lamb named Willy");
        strList.add("Mary had a little ham");
        strList.add("Mary had a sandwich");
        strList.add("Old McDonald had a farm named Willy");
        strList.add("Willy had a little dog named ham");
        strList.add("Willy had a big dog named Willy");
        strList.add("Willy had a huge dog named Willy");
        strList.add("(abcd)");
        strList.add("(xyz)");
        strList.add("Visit Target Store");
        strList.add("Visit Walmart Store");

        Trie<Character> trie = new Trie<>();
        //using another trie for suffix to avoid counting suffix as prefix and vice versa
        Trie<Character> reversedTrie = new Trie<>();
        for (String string : strList) {
            int length = string.length();
            Character[] words = new Character[length];
            Character[] reversed = new Character[length];
            for (int i = 0; i < length; i++) {
                int reversedIndex = length - 1 - i;
                words[i] = string.charAt(i);
                reversed[reversedIndex] = string.charAt(i);
            }
            trie.insert(words);
            reversedTrie.insert(reversed);
        }
        Map<Collection<Character>, Integer> prefixCount = trie.countPrefixes(Deque::addFirst);
        System.out.println("Prefixes:");
        printMap(prefixCount);
        System.out.println();
        Map<Collection<Character>, Integer> suffixCount = reversedTrie.countPrefixes(Deque::addLast);
        System.out.println("Suffixes:");
        printMap(suffixCount);
    }

    private static void printMap(Map<Collection<Character>, Integer> map) {
        map.entrySet()
                .stream()
                .filter(entry -> entry.getValue() > 1)
                .forEach(e -> {
                    String key = e.getKey().stream().map(Object::toString).collect(Collectors.joining(""));
                    String line = String.format("%s -> %d", key, e.getValue());
                    System.out.println(line);
                });
    }
}
```



------------
    
    
## Answer \#3

 Vote: 0

Created at 2022-03-17 06:02:27

------------

I think the solution provided by @Abhinav should work using HashMap. Here I will post the solution using a simple Trie implementation in java (with some customization, such as adding freq into the Trie Node).
```
ArrayList<String> strList = new ArrayList<String>();
    strList.add("Mary had a little lamb named Willy");
    strList.add("Mary had a little ham");
    strList.add("Old McDonald had a farm named Willy");
    strList.add("Willy had a little dog named ham");
    strList.add("( abc )");
    strList.add("( xyz )");
    strList.add("Visit Target Store");
    strList.add("Visit Walmart Store");

    TNode root = new TNode("");
    int maxFreq = 1;
    for(String sentence : strList) {
        TNode currentNode = root;
        String[] words = sentence.split(" "); // Assuming space character is the delimiter 
        for(String word: words) {
            if(currentNode.children.containsKey(word)) {
                currentNode.children.get(word).freq += 1;
                maxFreq = Math.max(maxFreq, currentNode.children.get(word).freq);
            } else {
                TNode c = new TNode(word);
                c.freq = 1;
                currentNode.children.put(word, c);
            }
            currentNode = currentNode.children.get(word);
        }
    }

    Map<String, Integer> result = new HashMap<String, Integer>();
    Queue<NodeWithPrefix> queue = new LinkedList<NodeWithPrefix>();
    for(TNode node : root.children.values()){
        NodeWithPrefix nwp = new NodeWithPrefix(node);
        nwp.prefix = "";
        queue.add(nwp);
    }
    while(!queue.isEmpty()) {
        NodeWithPrefix item = queue.poll();
        if(item.node.freq == maxFreq) {
            result.put(item.prefix + " " + item.node.value, item.node.freq);
        }
        for(TNode child : item.node.children.values()) {
            NodeWithPrefix nwp = new NodeWithPrefix(child);
            nwp.prefix = item.prefix + " " + item.node.value;
            queue.add(nwp);
        }
    }
    return result;
```

Here are 2 other classes required for this algorithm:
```
class NodeWithPrefix {
    String prefix;
    TNode node;
    public NodeWithPrefix(TNode node){
        this.node = node;
    }
}

class TNode {
    String value;
    int freq = 0;
    Map<String, TNode> children;
    public TNode(String value){
        this.value = value;
        children = new HashMap<String, TNode>();
    }
}
```

The output is for prefix: (for postfix should be similar, just need to build the Trie backward)
```
{ Mary had=2,  Mary had a=2,  Visit=2,  (=2,  Mary had a little=2,  Mary=2}
```

Here I am using a BFS to retrieve all sub strings having frequency equal maxFreq in the Trie. We can adjust the filter condition based on the need. You can do the DFS here also.
Other consideration is we can add prefix into the TNode class itself, I prefer to keep it separate in another class.


------------
    
    