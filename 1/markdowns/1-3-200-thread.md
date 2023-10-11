
# Post \#58575970 [Link](https://stackoverflow.com/questions/58575970/)

## Subprocess call with exit status 128

**Vote**: 11 (370/702) **Views**: 28256 (239/702) 

**Internal ID** \#1-3-200

Created at 2019-10-27 01:25:44

Tags: `python` `git` `subprocess`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

Essentially, I am trying to use a subprocess call to checkout a git commit at a specific sha hash.

However, I keep getting the error `subprocess.CalledProcessError: Command '['git', 'checkout', '62bbce43e']' returned non-zero exit status 128.`

This is my code below:

```
with open(filename) as inputfile:
    reader = csv.reader(inputfile, delimiter=",")
    linecount = 0
    for row in reader:
        if linecount == 0:
            linecount += 1
        else:
            repo = str(row[0])
            sha = str(row[2])
            specificfile = str(row[3])
            linenum = int(row[4])
            cl("cd", repo)
            subprocess.check_output(['git', 'checkout', sha])
            print("checkout done")
            git("checkout", "-")
```



----------
        
## Answer \#0

**Accepted** Vote: 5

Created at 2019-10-27 01:32:08

------------

A `subprocess.check_output()` call actually returns the output (and you can also get error output as well by passing a `stderr` parameter). You may want to have a look at that to see if it gives you an error explaining what happened.

Since you're getting an exception (meaning the call is not completing hence output may not be returned), you should be able to get the output from one of the exception members:

```
try:
    output = subprocess.check_output(['git', 'checkout', sha], stderr=subprocess.STDOUT)
except subprocess.CalledProcessError as e:
    print("Exception on process, rc=", e.returncode, "output=", e.output)
```



---



One thing I  know is that some `git` commands tend to return 128 if you're not actually  a Git repo. So I'd be looking at the path following your `cl("cd", repo)` line, with:

```
os.system("pwd")  # use "cd" for Windows.
```


If that `cd` of yours is running in a  that will  affect the current process and therefore you may not necessarily be in a Git repo at all. That would certainly explain the `128` return code.

By way of example, the following transcript shows what happens when I try to run a `git` command outside of a repo:

```
>>> try:
...     output = subprocess.check_output(['git', 'checkout', '12345'])
... except subprocess.CalledProcessError as e:
...     print(e.returncode, e.output)
...

128 b'fatal: not a git repository (or any of the parent directories): .git\n'
```


If it turns out you  in the wrong directory (i.e., the `cl("cd", repo)` statement is running a sub-process to change directory), you should use the Python-blessed method to change directories :

```
import os
os.chdir(path)
```


That actually changes the directory for the  process (the Python interpreter) rather than a transient sub-process.


---



 This is actually good advice in general - use Python-specific stuff as much as possible (since it's mostly cross-platform) rather than spawning a sub-shell (which is inherently platform-specific).


------------
    
    
## Answer \#1

 Vote: 3

Created at 2022-07-29 18:19:37

------------

For me, running the program from `inside the git repo` solved the issue.


------------
    
    
## Answer \#2

 Vote: 2

Created at 2019-10-27 05:40:21

------------

Besides the other point—that causing one sub-shell commands to `chdir` to some other directory does not affect subsequent separate sub-shell or sub-process commands, while calling `os.chdir` directly affects  process and therefore affects its subprocesses—note that you have two additional options here:
- The `subprocess` functions all accept a keyword `cwd` argument, whose default is `cwd=None`.  Providing a string here causes Python to `os.chdir` into the specified directory just for that one subprocess call.See the details [here](https://docs.python.org/3/library/subprocess.html#popen-constructor).- Git itself provides a flag, `-C`, that tells Git to do its own `chdir` early on.  To invoke `git checkout` as if it were `cd path/to/repo; git checkout`, use `git -C path/to/repo checkout`.This flag was new in Git version 1.8.5, so if your Git is older than that, you won't have `git -C` (but if your Git is older than 2.x it's long past time to upgrade :-) ).


------------
    
    
## Answer \#3

 Vote: 0

Created at 2022-09-02 13:12:09

------------

For me the issue was that `git clone` was being called but that the library that I was cloning already existed. So the tool I was using was calling
```
git clone https://somevendor.org/my-repo
```

but `my-repo` was already a non-empty directory that existed. Deleting the existing folder solved the issue.


------------
    
    