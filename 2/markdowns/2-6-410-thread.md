
# Post \#75299523 [Link](https://stackoverflow.com/questions/75299523/)

## How to handle arrays of extremely large strings in C#

**Vote**: 2 (631/702) **Views**: 294 (702/702) 

**Internal ID** \#2-6-410

Created at 2023-01-31 15:38:57

Tags: `c#` `arrays` `memory-management` `out-of-memory` `floyd-warshall`

----------

#### Metadata:

Area: `Back-end`

Language: `c#` (full parsed tag list: `c#`)

----------

**Notepad**


----------

I'm implementing the Floyd-Warshal algorithm, as can be found [here](https://www.geeksforgeeks.org/floyd-warshall-algorithm-dp-16).
I'm not just interested in the shortest distance between nodes, but also in the path, corresponding with that distance.
In order to do this, I have modified the algorithm as follows:
```
double[,] dist = new double[V, V];        // existing line
string[,] connections = new string[V, V]; // new line, needed for remembering the path

...
for (i = 0; i < V; i++){
  for (j = 0; j < V; j++){
    dist[i, j] = graph[i, j];
    connections[i, j] = $"({i},{j})";}} // added: initialisation of "connections"

...
if (dist[i, k] + dist[k, j] < dist[i, j])
{
  dist[i, j] = dist[i, k] + dist[k, j];
  connections[i, j] = connections[i, k] + "-" + connections[k, j]; // Added for remembering shortest path
}
```

I'm running this algorithm with a snake-like list of locations of one million, all of them simply being added one after the other.
As a result, my `connections` array looks as follows:
```
[0, 0]  "(0,0)"
    [0, 1]  "(0,1)"
    [0, 2]  "(0,1)-(1,2)"
    [0, 3]  "(0,1)-(1,2)-(2,3)"
    [0, 4]  "(0,1)-(1,2)-(2,3)-(3,4)"
    [0, 5]  "(0,1)-(1,2)-(2,3)-(3,4)-(4,5)"
    ...
    [0, 787]  "(0,1)-(1,2)-...(786,787)" // length of +7000 characters
    ...
```

... at the moment of my `OutOfMemoryException` (what a surprise) :-)
I would like to avoid that `OutOfMemoryException`, and start thinking of different techniques:
- - 
I believe the second option being the most realistic (don't kill me if I'm wrong) :-)
Is there a technique in C# which makes that possible?
Oh, if you react like "", don't refrain from telling me how :-)

In the meantime, I've replaced my strings  with `List`s of `List`s of `Point`s, and this seems to be working fine:
Instead of:
```
string[,] l_connections;
```

I have:
```
List<List<List<Point>>> l_connections;
```

The speed has doubled, and when working with huge collections of dictionaries (+1000 entries of ...), I get a `System.OutOfMemoryException` only at ±800 entries instead of ±650.
That's already a huge improvement, but does anybody know to get even better?

There is following GC and GCSettings information:
```
System.GC.MaxGeneration:[2]
IsServerGC:[False]
LargeObjectHeapCompactionMode:[Default]
LatencyMode:[Interactive]
```

I have altered the `LargeObjectHeapCompactionMode` to `CompactOnce` but this brought the performance back to similar results as when I was working with large strings instead of `List`s.

Hereby the code, when working with `List` collections:
```
public void floydWarshall(Dictionary<(int x, int y), double> dictionary, out double[,] dist, out List<List<List<Point>>> connections)
{
    int dictionary_size = (int)Math.Ceiling(Math.Sqrt(dictionary.Count));
    dist = new double[dictionary_size, dictionary_size];
...

    for (k = 0; k < dictionary_size; k++)
    {
        // Pick all vertices as source one by one
        for (i = 0; i < dictionary_size; i++)
        {
            // Pick all vertices as destination for the above picked source
            for (j = 0; j < dictionary_size; j++)
            {
                // If vertex k is on the shortest path from i to j, then update dist[i][j]
                if (dist[i, k] + dist[k, j] < dist[i, j])
                {
                    dist[i, j] = dist[i, k] + dist[k, j];
                    connections[i][j] =  new List<Point>();
                    connections[i][j].AddRange(connections[i][k]);
                    connections[i][j].AddRange(connections[k][j]);
                 }
            }
        }
    }
```

Thanks in advance


----------
        
## Answer \#0

**Accepted** Vote: 4

Created at 2023-02-28 17:08:02

------------

> Oh, if you react like "You're an idiot! There's a far better way to keep the shortest paths in Floyd-Warshal!", don't refrain from telling me how :-)
There is indeed.
Every shortest path from `m` to `n` is either a direct step `m-n` or a path through `k`, `m-...-k-...-n`, where `m-...-k` is the shortest path from `m` to `k`, already stored at array index `[m,k]` and `k-...-n` is the shortest path from `k` to `n`, already stored at array index `[k,n]`.
So all you need to store at `[m,n]` is the value of `k`, which is  single interior vertex along the shortest path.  (The `k` variable in the question code is a valid choice)
Storage requirement: `O(V^2)`, down from `O(V^3)`.
A suitable C# data structure would be `int[,]`, where
- `path[m,n] == m`- `path[m,n] == n``null``int?[,]`- `path[m,n] == k && k != m && k != n``k``[m,k]``[k,n]`

---


```
List<int> GetPathList(int n, int m, List<int> buffer = null)
{
    if (buffer == null) {
        buffer = new List<int>();
        buffer.Add(n);
    }
    if (n == m) return buffer;
    int k = path[n,m];
    if (k == n) {
        buffer.Add(m);
        return buffer;
    }
    if (k == m) return null;
    if (null == GetBuffer(n,k,buffer)) return null;
    return GetBuffer(k,m,buffer);
}
string GetPathString(m,n) => string.Join('-', GetPathList(n,m));
```



------------
    
    
## Answer \#1

 Vote: 1

Created at 2023-02-27 20:27:21

------------

For shortest distance and paths you can use [modified Dijkstra algoritm](https://%20https://github.com/eocron/Algorithm/tree/master/Algorithm/Graphs). Works fast, can be offloaded, pretty much any dynamic algorithm solution in general. It also support infinite graphs (which evaluate at runtime) and can work like A* search algorithm (shortest path in visited verticies, they work like this in games like minecraft).
I had implemented it some time ago and it looks like [this](https://dreampuf.github.io/GraphvizOnline/#digraph%20G%20%7B%0D%0Anode%20%5Bshape%3Dcircle%5D%3B%0D%0A0%20%5Bfontcolor%3D%22%23008000FF%22%2C%20color%3D%22%23008000FF%22%5D%3B%0D%0A1%3B%0D%0A2%3B%0D%0A3%3B%0D%0A4%3B%0D%0A5%3B%0D%0A6%20%5Bfontcolor%3D%22%23008000FF%22%2C%20color%3D%22%23008000FF%22%5D%3B%0D%0A7%3B%0D%0A8%3B%0D%0A9%20%5Bfontcolor%3D%22%23008000FF%22%2C%20color%3D%22%23008000FF%22%5D%3B%0D%0A10%3B%0D%0A11%3B%0D%0A12%3B%0D%0A13%3B%0D%0A14%3B%0D%0A15%3B%0D%0A16%20%5Bfontcolor%3D%22%23008000FF%22%2C%20color%3D%22%23008000FF%22%5D%3B%0D%0A17%3B%0D%0A18%20%5Bfontcolor%3D%22%23008000FF%22%2C%20color%3D%22%23008000FF%22%5D%3B%0D%0A19%3B%0D%0A20%3B%0D%0A21%3B%0D%0A22%3B%0D%0A23%20%5Bfontcolor%3D%22%23008000FF%22%2C%20color%3D%22%23008000FF%22%5D%3B%0D%0A24%3B%0D%0A25%3B%0D%0A26%3B%0D%0A27%3B%0D%0A28%3B%0D%0A29%20%5Bfontcolor%3D%22%23008000FF%22%2C%20shape%3Ddoublecircle%2C%20color%3D%22%23008000FF%22%5D%3B%0D%0A0%20-%3E%201%3B%0D%0A0%20-%3E%202%3B%0D%0A0%20-%3E%203%3B%0D%0A0%20-%3E%204%3B%0D%0A0%20-%3E%205%3B%0D%0A0%20-%3E%206%20%5Bfontcolor%3D%22%23008000FF%22%2C%20color%3D%22%23008000FF%22%5D%3B%0D%0A1%20-%3E%202%3B%0D%0A2%20-%3E%203%3B%0D%0A3%20-%3E%204%3B%0D%0A3%20-%3E%205%3B%0D%0A3%20-%3E%206%3B%0D%0A3%20-%3E%207%3B%0D%0A3%20-%3E%208%3B%0D%0A4%20-%3E%205%3B%0D%0A5%20-%3E%206%3B%0D%0A5%20-%3E%207%3B%0D%0A6%20-%3E%207%3B%0D%0A6%20-%3E%208%3B%0D%0A6%20-%3E%209%20%5Bfontcolor%3D%22%23008000FF%22%2C%20color%3D%22%23008000FF%22%5D%3B%0D%0A6%20-%3E%2010%3B%0D%0A6%20-%3E%2011%3B%0D%0A6%20-%3E%2012%3B%0D%0A6%20-%3E%2013%3B%0D%0A7%20-%3E%208%3B%0D%0A7%20-%3E%209%3B%0D%0A7%20-%3E%2010%3B%0D%0A7%20-%3E%2011%3B%0D%0A7%20-%3E%2012%3B%0D%0A8%20-%3E%209%3B%0D%0A9%20-%3E%2010%3B%0D%0A9%20-%3E%2011%3B%0D%0A9%20-%3E%2012%3B%0D%0A9%20-%3E%2013%3B%0D%0A9%20-%3E%2014%3B%0D%0A9%20-%3E%2015%3B%0D%0A9%20-%3E%2016%20%5Bfontcolor%3D%22%23008000FF%22%2C%20color%3D%22%23008000FF%22%5D%3B%0D%0A10%20-%3E%2011%3B%0D%0A10%20-%3E%2012%3B%0D%0A11%20-%3E%2012%3B%0D%0A11%20-%3E%2013%3B%0D%0A12%20-%3E%2013%3B%0D%0A12%20-%3E%2014%3B%0D%0A12%20-%3E%2015%3B%0D%0A12%20-%3E%2016%3B%0D%0A12%20-%3E%2017%3B%0D%0A13%20-%3E%2014%3B%0D%0A13%20-%3E%2015%3B%0D%0A13%20-%3E%2016%3B%0D%0A14%20-%3E%2015%3B%0D%0A14%20-%3E%2016%3B%0D%0A14%20-%3E%2017%3B%0D%0A15%20-%3E%2016%3B%0D%0A15%20-%3E%2017%3B%0D%0A16%20-%3E%2017%3B%0D%0A16%20-%3E%2018%20%5Bfontcolor%3D%22%23008000FF%22%2C%20color%3D%22%23008000FF%22%5D%3B%0D%0A16%20-%3E%2019%3B%0D%0A16%20-%3E%2020%3B%0D%0A16%20-%3E%2021%3B%0D%0A18%20-%3E%2019%3B%0D%0A18%20-%3E%2020%3B%0D%0A18%20-%3E%2021%3B%0D%0A18%20-%3E%2022%3B%0D%0A18%20-%3E%2023%20%5Bfontcolor%3D%22%23008000FF%22%2C%20color%3D%22%23008000FF%22%5D%3B%0D%0A18%20-%3E%2024%3B%0D%0A18%20-%3E%2025%3B%0D%0A18%20-%3E%2026%3B%0D%0A19%20-%3E%2020%3B%0D%0A19%20-%3E%2021%3B%0D%0A19%20-%3E%2022%3B%0D%0A19%20-%3E%2023%3B%0D%0A19%20-%3E%2024%3B%0D%0A20%20-%3E%2021%3B%0D%0A20%20-%3E%2022%3B%0D%0A20%20-%3E%2023%3B%0D%0A21%20-%3E%2022%3B%0D%0A23%20-%3E%2024%3B%0D%0A23%20-%3E%2025%3B%0D%0A23%20-%3E%2026%3B%0D%0A23%20-%3E%2027%3B%0D%0A23%20-%3E%2028%3B%0D%0A23%20-%3E%2029%20%5Bfontcolor%3D%22%23008000FF%22%2C%20color%3D%22%23008000FF%22%5D%3B%0D%0A24%20-%3E%2025%3B%0D%0A24%20-%3E%2026%3B%0D%0A24%20-%3E%2027%3B%0D%0A24%20-%3E%2028%3B%0D%0A24%20-%3E%2029%3B%0D%0A25%20-%3E%2026%3B%0D%0A25%20-%3E%2027%3B%0D%0A25%20-%3E%2028%3B%0D%0A25%20-%3E%2029%3B%0D%0A27%20-%3E%2028%3B%0D%0A27%20-%3E%2029%3B%0D%0A28%20-%3E%2029%3B%0D%0A%7D) visually, you can play around in my unit test section, it will visualize your graph in Graphviz format URL for better debugging experience.
My implementation pros:
- - - - - - - - - - 
Works like this:
```
[Test]
    [TestCase(new[] {1, 1, 1, 1}, 3)]
    [TestCase(new[] {2, 1, 1, 1}, 2)]
    [TestCase(new[] {1, 2, 3, 4}, 2)]
    [TestCase(new[] {4, 3, 2, 1}, 1)]
    [TestCase(new[] {2, 2, 1, 3, 3, 2, 1}, 3)]
    [TestCase(new[] {2, 0, 1, 1}, 1)]
    public void PathToNearCity(int[] cities, int expectedMinSteps)
    {
        var graph = ParsePathToRome(cities);
        var source = 0;
        var targets = cities
            .Select((x, i) => new {x, i})
            .Where(x => x.x == 0)
            .Select(x => x.i)
            .Concat(new[] {cities.Length - 1})
            .ToList();
        var result = new InfiniteDijkstraAlgorithm<int, int>(
            x => graph.OutEdges(x).Select(y => y.Target),
            x => 0,
            (x, y) => x.Weight + 1);
        result.Search(source);
        var target = targets.OrderBy(x => result.GetWeight(x)).First();
        var pathToRome = result.GetPath(source, target).ToList();
        Print(graph, pathToRome);
        Assert.AreEqual(expectedMinSteps, result.GetWeight(target));
    }
```

I also could calculate Levenstain distance/paths:
```
[Test]
    [TestCase("kitten", "sitting", 3)]
    [TestCase("kitten", "kitting", 2)]
    [TestCase("hello", "kelm", 3)]
    [TestCase("asetbaeaefasdfsa", "asdfaew", 12)]
    [TestCase("aaaa", "a", 3)]
    [TestCase("a", "a", 0)]
    [TestCase("a", "b", 1)]
    public void LevenstainDistance(string sourceStr, string targetStr, int expectedMinDistance)
    {
        var source = Tuple.Create(0, 0);
        var target = Tuple.Create(sourceStr.Length, targetStr.Length);
        var result = new InfiniteDijkstraAlgorithm<Tuple<int, int>, int>(
            x =>
            {
                var list = new List<Tuple<int, int>>();
                if (x.Item2 < targetStr.Length)
                    list.Add(Tuple.Create(x.Item1, x.Item2 + 1));
                if (x.Item1 < sourceStr.Length)
                    list.Add(Tuple.Create(x.Item1 + 1, x.Item2));
                if (x.Item1 < sourceStr.Length && x.Item2 < targetStr.Length)
                    list.Add(Tuple.Create(x.Item1 + 1, x.Item2 + 1));
                return list;
            },
            x => 0,
            (xw, y) =>
            {
                var x = xw.Vertex;
                if (x.Item1 < sourceStr.Length
                    && x.Item2 < targetStr.Length
                    && sourceStr[x.Item1] == targetStr[x.Item2]
                    && (y.Item1 - 1) == x.Item1
                    && (y.Item2 - 1) == x.Item2)
                {
                    return xw.Weight;
                }

                return xw.Weight + 1;
            },
            buildShortestPathTree: true);
        result.Search(source);

        var minDistance = result.GetWeight(target);
        Assert.AreEqual(expectedMinDistance, minDistance);
    }
```



------------
    
    
## Answer \#2

 Vote: 1

Created at 2023-02-27 21:30:32

------------

I doubt you are actually using all of the memory on the system. I suspect the actual problem is related to exhaustion of the  for the process. This is a long-standing issue with the .Net GC ([here's an example from 2011](https://stackoverflow.com/q/5250039/3043)), where actual memory is reclaimed, but the address table is often never compacted for items above a certain size, and therefore can cause OutOfMemory exceptions even when memory use is relatively sparse.
I've heard this is actually significantly improved in for recent versions of .Net Core, but I haven't done a deep dive on it yet myself and I still suspect this is our problem here. If so, the issue can be solved by changing the [GCSettings.LargeObjectHeapCollectionMode](https://learn.microsoft.com/en-us/dotnet/api/system.runtime.gcsettings.largeobjectheapcompactionmode) property.
I base the suspicion on this info in this question:
> ```
LargeObjectHeapCompactionMode:[Default]
```

When we check [the documentation](https://learn.microsoft.com/en-us/dotnet/api/system.runtime.gclargeobjectheapcompactionmode) on what that means we find this:
> Default   1   The large object heap (LOH) is not compacted.
Note: the only other option looks like this:
> CompactOnce   2
The large object heap (LOH) will be compacted during the next blocking generation 2 garbage collection.
See the "once" in that description? Also see the "next blocking generation 2"? Those don't happen often. This means you'll want to be explicit about forcing a full (blocking) Gen 2 collection after changing the property. You'll also want to be strategic about how you use this, as full Gen 2 collections are not speedy operations.


------------
    
    
## Answer \#3

 Vote: 0

Created at 2023-02-28 16:48:14

------------

One idea which allows you to reduce the memory footprint - but is kinda inconvenient:
don't store strings, every character is 2 byte, but you're only using characters from 0-9 + 4 separation characters "(", ")", "-", ",".
so 13 characters total so you only need less than 4 bit to encode a character.
write your own string class that has a byte[] and encodes 2 characters in each byte. (in this 4 bit is enough space for a termination character "\0").
so you'd have a class like:
```
public struct SmallString{
    
        private static char[] Charset = new []{'\0', '0', '1', '2', '3', '4', '5', '6', '7', '8', '9', '(', ')', '-', ','};
    
        private byte[] data;
    
        public static SmallString FromString(string input){
    
            var data = new byte[(input.Length+1) / 2];
    
            int pos = 0;
            byte current = 0;
            bool second = false;
            foreach(var ch in input){
    
                var idx = Array.IndexOf(Charset, ch);
                if(idx == -1) throw new Exception("Illegal Character");
                current |= (byte)(idx << (second ? 0 : 4));
    
                if(second){
                    data[pos] = current;
                    current = 0;
                    second = false;
                    pos++;
                }else{
                    second = true;
                }
            }
            if(second){
                data[pos] = current;   
            }
            
            return new SmallString(){data = data};
        }
    
        public override string ToString(){
            // determine length:
            var length = data.Length * 2;
            // if the last half byte is empty/0 reduce length by 1
            //if((data[^1] & 0b1111) == 0) length--;
    
            return string.Create(length, data, (span, state) =>
            {
                int i = 0;
                foreach (byte b in state)
                {
                    span[i++] = Charset[(b >> 4) & 0x0F];
                    span[i++] = Charset[b & 0x0F];
                }
            });
        }
    
    }
```

this would be a reduction from 4 bytes to 1 byte (4x).
// note that ")" is always followed by a "-", so that could be reduced, too, but makes the implementation more complicated. and for the 10 digits you also need at least 4 byte so you would not save anything.
// you could store the number pairs using LEB128 for the numbers, that could be even smaller, than this approach.
Since all your strings are also quite similar, it would also be possible to create a lookup table, (e.g. your minimum string size is 5, which would be 10 byte, which kinda reappears in all partial pathes of a given path)
so you could create a list for the first let's say 65535 strings that are 10 characters or longer and store them in that static list.
your custom string implementation would then be an array of 'shorts' (containing the indices to the prefix-strings) + an actual short string for the suffix.
so instead of storing 10 characters (20 bytes) it would store 2 bytes (a short). (a reduction of 10x)

---


edit:
to follow up on my notes above, using LEB128 to store the numbers, and storing them as tuples, using the following LEB128 struct yields an even higher gain, compared to the smallString struct.
storing "(0,1)-(1,2)-(2,3)-(3,4)-(4,5)"...
- - - - 
for the LEB128 usage:
```
// store as:
List<(LEB128, LEB128)> bs = new (){ (0,1),(1,2),(2,3),(3,4),(4,5) };

// when printout is necessary:
Console.WriteLine(string.Join('-', bs).Replace(" ", ""));
```

```
public struct LEB128
{
    private byte[] data;
    public int Length => data.Length;
    
    private static List<byte> bytes = new(); // probably should be threadlocal
    public static LEB128 Encode(int number)
    {
        const int bitsPerByte = 7;
        const int continuationBit = 0x80;
        const int valueMask = continuationBit - 1;

        bytes.Clear();
        do
        {
            byte byteValue = (byte)(number & valueMask);
            number >>= bitsPerByte;
            if (number != 0)
            {
                byteValue |= (byte)continuationBit;
            }
            bytes.Add(byteValue);
        } while (number != 0);

        return new (){data = bytes.ToArray()};
    }
    
    public static implicit operator LEB128(int n) => LEB128.Encode(n);
    
    public int Decode() => LEB128.Decode(data);
    
    public static int Decode(byte[] bytes)
    {
        const int bitsPerByte = 7;
        const int valueMask = (1 << bitsPerByte) - 1;

        int result = 0;
        int shift = 0;
        int index = 0;

        while (index < bytes.Length)
        {
            int byteValue = bytes[index] & valueMask;
            result |= byteValue << shift;
            shift += bitsPerByte;

            if ((bytes[index] & 0x80) == 0)
            {
                break;
            }

            index++;
        }

        if (index == bytes.Length && (bytes[index - 1] & 0x80) != 0)
        {
            throw new ArgumentException("Invalid LEB128 encoding: the last byte has the continuation bit set.");
        }

        return result;
   }

    public override string ToString()
    {
        return this.Decode().ToString();
    }
}
```



------------
    
    
## Answer \#4

 Vote: 0

Created at 2023-03-01 09:40:31

------------

I had more o less the same problem when I wanted to calculated all paths between the nodes of a graph. The problem was that some of our graphs was a mesh graph and finding all path between nodes in mesh graph is theoretically a  problem. So no matter what algorithm you use it will be hugely time consuming and memory intensive. Luckily in my case we were just interested in some specific path between some specific nodes. So I added some logic to ignore some paths and just find the ones that we interested. It had huge impacts on the performance. So if you think about your problem again maybe you can ignore some nodes and paths and reduce the algorithm complexity.


------------
    
    