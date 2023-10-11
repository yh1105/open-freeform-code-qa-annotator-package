
# Post \#70572447 [Link](https://stackoverflow.com/questions/70572447/)

## Select all characters in a string until a specific character Ruby

**Vote**: 1 (672/702) **Views**: 1240 (642/702) 

**Internal ID** \#2-9-484

Created at 2022-01-03 23:15:45

Tags: `ruby` `string` `visual-studio`

----------

#### Metadata:

Area: `Back-end`

Language: `ruby` (full parsed tag list: `ruby`)

----------

**Notepad**


----------

So what I want is to get n characters until it hits a specific character.
i have this String :
```
a='2.452811139617034,42.10874821716908|3.132087902867818,42.028314077306646|-0.07934861041448178,41.647538468746916|-0.07948265046522918,41.64754863599606'
```

How can I make it to only get to the character | , but without getting that character,
and get it like this:
```
2.452811139617034,42.10874821716908
```



----------
        
## Answer \#0

**Accepted** Vote: 3

Created at 2022-01-04 01:59:39

------------

You can avoid creating an unnecessary Array (like `Array#split`) or using a Regex (like `Array#gsub`) by using.
```
a = "2.452811139617034,42.10874821716908|3.132087902867818,42.028314077306646|-0.07934861041448178,41.647538468746916|-0.07948265046522918,41.64754863599606"

a[0,a.index('|')]
#=>"2.452811139617034,42.1087482171"
```

This means select characters at positions 0 up to the index of the first pipe (`|`). Technically speaking it is start at position 0 and select the length of n where n is the index of the pipe character which works in this case because ruby uses 0 based indexing.
As @CarySwoveland astutely pointed out the string may not contain a pipe in which case my solution would need to change to
```
#to return entire string
a[0,a.index('|') || a.size]
# or
b = a.index(?|) ? a[0,b] : a
# or to return empty string
a[0,a.index('|').to_i]
# or to return nil
a[0,a.index(?|) || -1]
```



------------
    
    
## Answer \#1

 Vote: 3

Created at 2022-01-03 23:52:25

------------

You can simply do it like this:
`a.split('|').first`


------------
    
    
## Answer \#2

 Vote: 2

Created at 2022-01-04 03:07:55

------------

```
a[/[^|]+/]
  #=> "2.452811139617034,42.10874821716908"
```

The regular expression simply matches as many characters other than `'|'` that it can.


------------
    
    
## Answer \#3

 Vote: 2

Created at 2022-01-04 04:01:44

------------

I'm always curious as to the performance of various options and so I took the liberty of looping through some of the approaches suggested.  I named them as follows:
```
split_all = a.split('|').first
partition = a.partition('|').first
split_two = a.split('|', 2).first
string_brack_args = a[0,a.index('|')]
string_brack_range = a[0...a.index('|')]
gsub_regex = a.gsub(/\|.*$/, "")
plain_regex = a[/[^|]+/]
```


---


The results were as follows (slowest at top):
```
user     system      total        real
gsub_regex           0.170000   0.000000   0.170000 (  0.162666)
split_all            0.110000   0.000000   0.110000 (  0.109498)
split_two            0.040000   0.000000   0.040000 (  0.041792)
partition            0.040000   0.000000   0.040000 (  0.037161)
string_brack_range   0.030000   0.000000   0.030000 (  0.034021)
plain_regex          0.040000   0.000000   0.040000 (  0.033468)
string_brack_args    0.020000   0.000000   0.020000 (  0.022455)
```

That's nearly an eight-fold increase in efficiency between the slowest and fastest.  Even some of the little things make a huge difference.  Granted, I looped through these 100_000 times, so the difference is pretty small for the one instance, but even something as simple as using using `split(str, 2)` vs `split(str)` is well over twice as fast.  In fact, the faster half of the approaches listed average 3 times as fast as the slower half.


------------
    
    
## Answer \#4

 Vote: 0

Created at 2022-01-03 23:35:33

------------

One approach uses a `gsub` replacement:
```
a = "2.452811139617034,42.10874821716908|3.132087902867818,42.028314077306646|-0.07934861041448178,41.647538468746916|-0.07948265046522918,41.64754863599606"
output = a.gsub(/\|.*$/, "")
puts output  # 2.452811139617034,42.10874821716908
```



------------
    
    