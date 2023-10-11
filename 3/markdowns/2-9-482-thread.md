
# Post \#71332602 [Link](https://stackoverflow.com/questions/71332602/)

## Upgrading to Ruby 3.1 causes Psych::DisallowedClass exception when using YAML.load_file

**Vote**: 33 (185/702) **Views**: 12127 (393/702) 

**Internal ID** \#2-9-482

Created at 2022-03-03 05:44:50

Tags: `ruby` `yaml` `activesupport`

----------

#### Metadata:

Area: `Back-end`

Language: `ruby` (full parsed tag list: `ruby` `yaml`)

----------

**Notepad**


----------

When upgrading to ruby 3.1, I am seeing the following sort error message when using `YAML.load_file some_file_name`
```
Psych::DisallowedClass:
   Tried to load unspecified class: Matrix
```

Other load statements cause similar errors but cite different unspecified classes e.g. OpenStruct.  It appears that the latest version of YAML only loads classes from a permitted white list, so it is necessary to use a permitted_class keyword to allow other classes. I have tried
```
hsh = YAML.load_file some_file_name, permitted_classes: [Matrix, OpenStruct]
```

but this gives the error
```
Psych::DisallowedClass:
   Tried to load unspecified class: Symbol
```

how do I fix this?


----------
        
## Answer \#0

**Accepted** Vote: 26

Created at 2022-03-03 06:26:52

------------

`Symbol` is also not allowed per default. Therefore just add `Symbol` to the `permitted_classes` too:
```
hash = YAML.load_file(
  some_file_name, 
  permitted_classes: [Matrix, OpenStruct, Symbol]
)
```

See the list of default [permitted_classes](https://ruby-doc.org/stdlib-3.1.1/libdoc/psych/rdoc/Psych.html#method-c-safe_load).


------------
    
    
## Answer \#1

 Vote: 31

Created at 2022-07-14 08:06:25

------------

The working solution is to add this line to config/application.rb
```
config.active_record.yaml_column_permitted_classes = [ActiveSupport::HashWithIndifferentAccess]
```

You can do the same with any class name, like
```
config.active_record.yaml_column_permitted_classes = [Symbol, Hash, Array, ActiveSupport::HashWithIndifferentAccess]
```



------------
    
    
## Answer \#2

 Vote: 15

Created at 2022-07-15 08:46:37

------------

Had this on rails 6.1 upgrade. If you have no other choice, maybe this workaround will bring you some time (application.rb):
```
config.active_record.use_yaml_unsafe_load = true
```



------------
    
    