
# Post \#52412297 [Link](https://stackoverflow.com/questions/52412297/)

## How to replace environment variable value in yaml file to be parsed using python script

**Vote**: 27 (213/702) **Views**: 84889 (99/702) 

**Internal ID** \#1-3-272

Created at 2018-09-19 18:39:30

Tags: `python` `python-2.7` `pyyaml`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I need to use environment variable "PATH" in yaml file which needs to be parsed with a script.  

This is the environment variable I have set on my terminal:

```
$ echo $PATH
/Users/abc/Downloads/tbwork
```


This is my sample.yml:

```
---
Top: ${PATH}/my.txt
Vars:
- a
- b
```


When I parse this yaml file with my script, I don't see `PATH` variables actual value. 

This is my script:

```
import yaml
import os
import sys

stream = open("sample.yml", "r")
docs = yaml.load_all(stream)
for doc in docs:
    for k,v in doc.items():
        print k, "->", v
    print "\n",
```


Output:

```
Top -> ${PATH}/my.txt
Vars -> ['a', 'b']
```


Expected output is:

```
Top -> /Users/abc/Downloads/tbwork/my.txt
Vars -> ['a', 'b']
```


Can someone help me figuring out the correct way to do it if I am doing it wrong way?


----------
        
## Answer \#0

**Accepted** Vote: 29

Created at 2018-09-19 19:13:41

------------

PY-yaml library doesn't resolve environment variables by default. You need to define an implicit resolver that will find the regex that defines an environment variable and execute a function to resolve it.
You can do it through `yaml.add_implicit_resolver` and `yaml.add_constructor`. In the code below, you are defining a resolver that will match on ${ env variable } in the YAML value and calling the function path_constructor to look up the environment variable.
```
import yaml
import re
import os

path_matcher = re.compile(r'\$\{([^}^{]+)\}')
def path_constructor(loader, node):
  ''' Extract the matched value, expand env variable, and replace the match '''
  value = node.value
  match = path_matcher.match(value)
  env_var = match.group()[2:-1]
  return os.environ.get(env_var) + value[match.end():]

yaml.add_implicit_resolver('!path', path_matcher)
yaml.add_constructor('!path', path_constructor)

data = """
env: ${VAR}/file.txt
other: file.txt
"""

if __name__ == '__main__':
  p = yaml.load(data, Loader=yaml.FullLoader)
  print(os.environ.get('VAR')) ## /home/abc
  print(p['env']) ## /home/abc/file.txt
```

 Do not run this if you are not the one specifying the env variables (or any other untrusted input) as there are remote code execution vulnerabilities with FullLoader as of July 2020.


------------
    
    
## Answer \#1

 Vote: 12

Created at 2019-03-22 13:53:29

------------

Here is an alternative version which does use a new Loader class if you do not want to modify the global/default yaml Loader.

And more importantly, it correctly replaces interpolated strings that are not just the environment variables, eg `path/to/${SOME_VAR}/and/${NEXT_VAR}/foo/bar`

```
path_matcher = re.compile(r'.*\$\{([^}^{]+)\}.*')
        def path_constructor(loader, node):
            return os.path.expandvars(node.value)

        class EnvVarLoader(yaml.SafeLoader):
            pass

        EnvVarLoader.add_implicit_resolver('!path', path_matcher, None)
        EnvVarLoader.add_constructor('!path', path_constructor)

        with open(configPath) as f:
            c = yaml.load(f, Loader=EnvVarLoader)
```



------------
    
    
## Answer \#2

 Vote: 6

Created at 2020-02-18 15:06:17

------------

There is a nice library [envyaml](https://pypi.org/project/envyaml/) for this.
With it it's very simple:

```
from envyaml import EnvYAML

# read file env.yaml and parse config
env = EnvYAML('env.yaml')
```



------------
    
    
## Answer \#3

 Vote: 1

Created at 2021-04-28 07:43:57

------------

You can see a how to [here](https://dev.to/mkaranasou/python-yaml-configuration-with-environment-variables-parsing-2ha6), which lead to the very small library [pyaml-env](https://pypi.org/project/pyaml-env/) for ease of use so that we don't repeat things in every project.
So, using the library, your sample yaml becomes:
```
---
Top: !ENV ${PATH}/my.txt
Vars:
- a
- b
```

and with `parse_config`
```
from pyaml_env import parse_config
config = parse_config('path/to/config.yaml')

print(config)
# outputs the following, with the environment variables resolved
{
    'Top': '/Users/abc/Downloads/tbwork/my.txt'
    'Vars': ['a', 'b']
}
```

There are also options to use default values if you wish, like this:
```
---
Top: !ENV ${PATH:'~/data/'}/my.txt
Vars:
- a
- b
```

About the implementation, in short:
For PyYAML to be able to resolve environment variables, we need three main things:

1. A regex pattern for the environment variable identification e.g. pattern = re.compile(‘.?${(\w+)}.?’)
2. A tag that will signify that there’s an environment variable (or more) to be parsed, e.g. !ENV.
3. And a function that the loader will use to resolve the environment variables


A full example:
```
import os
import re
import yaml


def parse_config(path=None, data=None, tag='!ENV'):
    """
    Load a yaml configuration file and resolve any environment variables
    The environment variables must have !ENV before them and be in this format
    to be parsed: ${VAR_NAME}.
    E.g.:
    database:
        host: !ENV ${HOST}
        port: !ENV ${PORT}
    app:
        log_path: !ENV '/var/${LOG_PATH}'
        something_else: !ENV '${AWESOME_ENV_VAR}/var/${A_SECOND_AWESOME_VAR}'
    :param str path: the path to the yaml file
    :param str data: the yaml data itself as a stream
    :param str tag: the tag to look for
    :return: the dict configuration
    :rtype: dict[str, T]
    """
    # pattern for global vars: look for ${word}
    pattern = re.compile('.*?\${(\w+)}.*?')
    loader = yaml.SafeLoader

    # the tag will be used to mark where to start searching for the pattern
    # e.g. somekey: !ENV somestring${MYENVVAR}blah blah blah
    loader.add_implicit_resolver(tag, pattern, None)

    def constructor_env_variables(loader, node):
        """
        Extracts the environment variable from the node's value
        :param yaml.Loader loader: the yaml loader
        :param node: the current node in the yaml
        :return: the parsed string that contains the value of the environment
        variable
        """
        value = loader.construct_scalar(node)
        match = pattern.findall(value)  # to find all env variables in line
        if match:
            full_value = value
            for g in match:
                full_value = full_value.replace(
                    f'${{{g}}}', os.environ.get(g, g)
                )
            return full_value
        return value

    loader.add_constructor(tag, constructor_env_variables)

    if path:
        with open(path) as conf_data:
            return yaml.load(conf_data, Loader=loader)
    elif data:
        return yaml.load(data, Loader=loader)
    else:
        raise ValueError('Either a path or data should be defined as input')
```



------------
    
    
## Answer \#4

 Vote: 1

Created at 2021-09-30 17:55:13

------------

You can run it like this on terminal.
```
ENV_NAME=test
cat << EOF > new.yaml
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ${ENV_NAME}
EOF

Then do a cat new.yaml
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: test
```



------------
    
    
## Answer \#5

 Vote: 0

Created at 2020-03-18 01:33:34

------------

Using yamls add_implicit_resolver and add_constructor works for me but like this with the above example:

```
import yaml
import re
import os
os.environ['VAR']="you better work"
path_matcher = re.compile(r'\$\{([^}^{]+)\}')
def path_constructor(loader, node):

  ''' Extract the matched value, expand env variable, and replace the match '''
  print("i'm here")
  value = node.value
  match = path_matcher.match(value)
  env_var = match.group()[2:-1]
  return os.environ.get(env_var) + value[match.end():]

yaml.add_implicit_resolver('!path', path_matcher, None, yaml.SafeLoader)
yaml.add_constructor('!path', path_constructor, yaml.SafeLoader)

data = """
env: ${VAR}/file.txt
other: file.txt
"""

if __name__ == '__main__':
  p = yaml.safe_load(data)
  print(os.environ.get('VAR')) ## you better work
  print(p['env']) ## you better work/file.txt
```



------------
    
    