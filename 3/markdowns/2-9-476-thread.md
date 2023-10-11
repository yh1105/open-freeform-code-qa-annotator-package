
# Post \#58896403 [Link](https://stackoverflow.com/questions/58896403/)

## .zshenv:2: command not found: rbenv

**Vote**: 8 (428/702) **Views**: 21074 (293/702) 

**Internal ID** \#2-9-476

Created at 2019-11-17 00:11:40

Tags: `ruby` `zsh` `rbenv` `zshrc`

----------

#### Metadata:

Area: `Back-end`

Language: `ruby` (full parsed tag list: `ruby`)

----------

**Notepad**


----------

When switching from bash to zsh, I looked up how to resolve an issue with my rbenv folder not being used correctly by zsh and found this: 

```
$ echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.zshenv
$ echo 'eval "$(rbenv init -)"' >> ~/.zshenv
$ echo 'source $HOME/.zshenv' >> ~/.zshrc
$ exec $SHELL
```


I ran all of these and seem to be using the correct rbenv folder now, but I get this error message whenever I open a new iTerm window: 

```
/Users/myname/.zshenv:2: command not found: rbenv
```


What am I doing wrong? Any help would be very appreciated.


----------
        
## Answer \#0

**Accepted** Vote: 10

Created at 2019-12-17 23:12:13

------------

You need to add two things to your `PATH`.
First `rbenv` itself and second the ruby `shims`.


# Part 1 rbenv




## Installation




### Homebrew



If you installed `rbenv` with `brew`,
then the `rbenv` executable should be linked to `/usr/local/bin/rbenv`.

See [homebrew installation documentation](https://github.com/rbenv/rbenv#homebrew-on-macos) for details.

Please add `/usr/local/bin` to your path `PATH`, if it is missing.

```
# in ~/.zshrc

export PATH=/usr/local/bin:$PATH
```



### Github Checkout



If you install `rbenv` via a Github checkout, then the `rbenv` executalbe should be stored in `~/.rbenv/bin`.

See [github installation documentation](https://github.com/rbenv/rbenv#basic-github-checkout) for details.

Please add `~/.rbenv/bin` to your path `PATH`, if it is missing.

```
# in ~/.zshrc

export PATH=$HOME/.rbenv/bin:$PATH
```



## Verfiy



Please verify that `rbenv` is in your path by calling `which rbenv`.
The installation path should be returend.


# Part 2 shims



Add the ruby `shims` to you path.

```
# in ~/.zshrc

eval "$(rbenv init -)"
```


Instead of the `eval "$(rbenv init -)"` command you can also add the `shims` folder directly.

```
# in ~/.zshrc

export RBENV_ROOT=$HOME/.rbenv
export PATH=$RBENV_ROOT/shims:/versions:$PATH
```



# Part 3 rbenv doctor



You might also run the `rbenv-doctor` script mentioned [here](https://github.com/rbenv/rbenv#homebrew-on-macos),
to check your installation.


------------
    
    
## Answer \#1

 Vote: 3

Created at 2020-08-13 13:22:11

------------

I had the same problem...
when I ran ruby or rbenv, I got this error "command not found"
try this:
```
echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.zshenv

echo 'eval "$(rbenv init - zsh)"' >> ~/.zshenv

echo 'export PATH="$HOME/.rbenv/plugins/ruby-build/bin:$PATH"' >> ~/.zshenv
```



------------
    
    
## Answer \#2

 Vote: 1

Created at 2020-12-16 07:30:56

------------

I had this same error. I could run `which rbenv` and `rbenv` just fine, but no matter what I would get `command not found: rbenv`. The issue was that I had `eval "$(rbenv init -)"` in my `~/.zshenv` and not my `~/.zshrc` file. You may still have the path to `rbenv` be added to `$PATH` within `~/.zshenv` for it to work.


------------
    
    