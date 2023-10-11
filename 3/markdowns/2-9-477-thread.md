
# Post \#66128681 [Link](https://stackoverflow.com/questions/66128681/)

## Ruby: BUILD FAILED (macOS 11.2 using ruby-build 20210119) Mac Big Sur

**Vote**: 18 (275/702) **Views**: 24729 (265/702) 

**Internal ID** \#2-9-477

Created at 2021-02-09 23:17:26

Tags: `ruby` `macos`

----------

#### Metadata:

Area: `Back-end`

Language: `ruby` (full parsed tag list: `ruby`)

----------

**Notepad**


----------

I looked at this [Ruby installation (2.2.2) fails in macOS Big Sur](https://stackoverflow.com/questions/65524323/ruby-installation-2-2-2-fails-in-macos-big-sur)
My macOS is Big Sur and the version I have is 11.2 and it was the closest I could find to the issue I'm having with my OS, I followed what I could by trying
```
CFLAGS="-Wno-error=implicit-function-declaration" rbenv install 2.5.3
```

and also
```
RUBY_CFLAGS=-DUSE_FFI_CLOSURE_ALLOC rbenv install 2.5.3
```

This is the output in my Terminal:
```
Downloading openssl-1.1.1i.tar.gz...
-> https://dqw8nmjcqpjn7.cloudfront.net/e8be6a35fe41d10603c3cc635e93289ed00bf34b79671a3a4de64fcee00d5242
Installing openssl-1.1.1i...
Installed openssl-1.1.1i to /Users/richard/.rbenv/versions/2.5.3

Downloading ruby-2.5.3.tar.bz2...
-> https://cache.ruby-lang.org/pub/ruby/2.5/ruby-2.5.3.tar.bz2
Installing ruby-2.5.3...

WARNING: ruby-2.5.3 is nearing its end of life.
It only receives critical security updates, no bug fixes.

ruby-build: using readline from homebrew
/opt/homebrew/bin/ruby-build: line 1121: 31528 Killed: 9               "$RUBY_BIN" -e '
    manager = ARGV[0]
    packages = {
      "apt-get" => Hash.new {|h,k| "lib#{k}-dev" }.update(
        "openssl" => "libssl-dev",
        "zlib" => "zlib1g-dev"
      ),
      "yum" => Hash.new {|h,k| "#{k}-devel" }.update(
        "yaml" => "libyaml-devel"
      )
    }

    failed = %w[openssl readline zlib yaml].reject do |lib|
      begin
        require lib
      rescue LoadError
        $stderr.puts "The Ruby #{lib} extension was not compiled."
      end
    end

    if failed.size > 0
      $stderr.puts "ERROR: Ruby install aborted due to missing extensions"
      $stderr.print "Try running `%s install -y %s` to fetch missing dependencies.\n\n" % [
        manager,
        failed.map { |lib| packages.fetch(manager)[lib] }.join(" ")
      ] unless manager.empty?
      $stderr.puts "Configure options used:"
      require "rbconfig"; require "shellwords"
      RbConfig::CONFIG.fetch("configure_args").shellsplit.each { |arg| $stderr.puts "  #{arg}" }
      exit 1
    end
  ' "$(basename "$(type -p yum apt-get | head -1)")" 1>&4 2>&1

BUILD FAILED (macOS 11.2 using ruby-build 20210119)

Inspect or clean up the working tree at /var/folders/rn/c7nmr3x12gg5r8qwsr4ty8hh0000gn/T/ruby-build.20210209143521.94730.xfFT9O
Results logged to /var/folders/rn/c7nmr3x12gg5r8qwsr4ty8hh0000gn/T/ruby-build.20210209143521.94730.log

Last 10 log lines:
installing bundled gems:            /Users/richard/.rbenv/versions/2.5.3/lib/ruby/gems/2.5.0 (build_info, cache, doc, extensions, gems, specifications)
                                    power_assert 1.1.1
                                    net-telnet 0.1.1
                                    did_you_mean 1.2.0
                                    xmlrpc 0.3.0
                                    rake 12.3.0
                                    minitest 5.10.3
                                    test-unit 3.2.7
installing rdoc:                    /Users/richard/.rbenv/versions/2.5.3/share/ri/2.5.0/system
installing capi-docs:               /Users/richard/.rbenv/versions/2.5.3/share/doc/ruby
```

I get this error for both commands mentioned above and both give this same output.
The version of Ruby also doesn't seem to matter, I've tried 3.0.0 as well and get the same results.
Additionally this is the original output when I try to just install ruby with `rbenv install`
```
Downloading openssl-1.1.1i.tar.gz...
-> https://dqw8nmjcqpjn7.cloudfront.net/e8be6a35fe41d10603c3cc635e93289ed00bf34b79671a3a4de64fcee00d5242
Installing openssl-1.1.1i...
Installed openssl-1.1.1i to /Users/richard/.rbenv/versions/2.5.3
Downloading ruby-2.5.3.tar.bz2...
-> https://cache.ruby-lang.org/pub/ruby/2.5/ruby-2.5.3.tar.bz2
Installing ruby-2.5.3...
WARNING: ruby-2.5.3 is nearing its end of life.
It only receives critical security updates, no bug fixes.
ruby-build: using readline from homebrew
BUILD FAILED (macOS 11.2 using ruby-build 20210119)
Inspect or clean up the working tree at /var/folders/rn/c7nmr3x12gg5r8qwsr4ty8hh0000gn/T/ruby-build.20210209143107.60561.YqaRpk
Results logged to /var/folders/rn/c7nmr3x12gg5r8qwsr4ty8hh0000gn/T/ruby-build.20210209143107.60561.log
Last 10 log lines:
compiling ../.././ext/psych/yaml/reader.c
compiling ../.././ext/psych/yaml/emitter.c
compiling ../.././ext/psych/yaml/parser.c
linking shared-object json/ext/generator.bundle
5 warnings generated.
linking shared-object date_core.bundle
linking shared-object zlib.bundle
1 warning generated.
linking shared-object psych.bundle
make: *** [build-ext] Error 2
```

xcode-select version is 2384.
Homebrew version is 3.0.0 and `brew doctor` say's I'm ready to brew.
My .zshrc file also contains this line `eval "$(rbenv init -)"`
At this point I'm not sure where else to turn 
If there is any specifics you want to see in the log file let me know where from, the log file is too big to share here. Why is this happening and how can I fix this? ‍♂️


----------
        
## Answer \#0

**Accepted** Vote: 17

Created at 2021-02-17 23:08:48

------------

. I'm sure the rbenv devs are working on an actual solution but this workaround should help others who are setting up their ruby environments on the new M1 chips for Mac.
- Make sure your Terminal is using Rosetta. You can find how to do that using Google.- Uninstall your current `rbenv` following these instructions [Removing rbenv](https://github.com/rbenv/rbenv#uninstalling-rbenv). Be sure you also remove all the downloaded versions of ruby if you have any (minus the system default) located in `/Users/<your user name>/.rbenv/versions/`.- Uninstall the ARM version of Homebrew with: `/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/uninstall.sh)"`- Install the x86_64 version of Homebrew with: `arch -x86_64 /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"`- If you run `brew install rbenv` should produce output saying "Error: Cannot install in Homebrew on ARM processor in Intel default prefix (/usr/local)!". This is expected.- You want to tell brew to install the older architecture x86_64 `arch -x86_64 brew install rbenv`- Then finally install the version you want using `arch -x86_64 rbenv install x.x.x` (x = some number i.e. 2.7.2)
From there you just need to remember to tell brew `arch -x86_64` when installing other versions of Ruby.
Once an actual fix comes through you'll be able to switch back to the newer architecture and not have to use the `arch` argument. You also don't have to do this all the time with brew either, just rbenv.


------------
    
    
## Answer \#1

 Vote: 17

Created at 2022-02-08 05:53:18

------------

While installing rbenv, I faced the same issue on .
This solution worked for me-
Instead of running this-
`rbenv install 2.5.3`
run this-
`RUBY_CFLAGS="-Wno-error=implicit-function-declaration" rbenv install 2.5.3`


------------
    
    
## Answer \#2

 Vote: 8

Created at 2021-03-17 17:08:15

------------

I was able to install Ruby 2.7.2 after the `readline openssl` installation
```
brew install readline openssl
rbenv install 2.7.2
```



------------
    
    
## Answer \#3

 Vote: 1

Created at 2021-05-09 23:48:58

------------

I'm using rvm. I read and tried the solutions discussed here [https://github.com/rvm/rvm/issues/5033](https://github.com/rvm/rvm/issues/5033).
However, the one that worked for me was `rvm install "ruby-2.6.3" --with-out-ext=fiddle`
In case you're using `rbenv` you can follow the discussion [here](https://github.com/rbenv/ruby-build/issues/1691)


------------
    
    