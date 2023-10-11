
# Post \#66242718 [Link](https://stackoverflow.com/questions/66242718/)

## Angular 11 CLI, "ng generate" option: --skip-tests

**Vote**: 9 (409/702) **Views**: 33401 (215/702) 

**Internal ID** \#0-0-106

Created at 2021-02-17 13:18:32

Tags: `angular-cli`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

When using the Angular 11 CLI, when I [generate](https://angular.io/cli/generate) a new component I can specify the option `--skip-tests` to avoid generating a `.component.spec.ts` at this stage.
```
ng generate component --name asdf --module app --skip-tests --dry-run
```

When I generate a new module containing a new component (`--route` option) I can't specify the `--skip-tests` option.
```
ng generate module --name asdf --module app --route asdf --routing true --skip-tests --dry-run
```

If I do I get the error:
```
Unknown option: '--skip-tests'
```

Is there another way to skip generating the test in this case or is it just not supported for `generate module`?


----------
        
## Answer \#0

**Accepted** Vote: 25

Created at 2021-02-17 13:26:24

------------

According to [this article](https://tutorialsforangular.com/2019/12/31/skipping-tests-in-angular-cli/), you can either add the `--skip-tests` flag upon generating the project as a whole, or generating individual components. From a logical perspective, it makes sense that you could not apply the flag on a module, since modules are not generated with test files.
To make the change for all future generated code, modify your `angular.json` file by adding the `skipTests:true` to the schematics section.
```
"schematics": {
        "@schematics/angular:component": {
          "style": "scss",
          "skipTests": true
        },
        "@schematics/angular:class": {
          "skipTests": true
        },
        "@schematics/angular:directive": {
          "skipTests": true
        },
        "@schematics/angular:guard": {
          "skipTests": true
        },
        "@schematics/angular:module": {
          "skipTests": true
        },
        "@schematics/angular:pipe": {
          "skipTests": true
        },
        "@schematics/angular:service": {
          "skipTests": true
        }
     }
```



------------
    
    
## Answer \#1

 Vote: 5

Created at 2021-02-17 13:25:04

------------

`--skip-tests` is not supported by the `ng generate module` command.
[supported options are :](https://angular.io/cli/generate#module)
- `--flat`- `--lint-fix`- `--module`- `--project`- `--route`- `--routing`- `--routing-scope`

an alternative is to use two commands sequencially:
```
ng generate module --name am && ng generate component --name ac --module am --skip-tests --dry-run
```

`&&` means Execute command2 only if the execution of command1 has finished successfully.
, possible workaround can be find [here](https://github.com/formulahendry/vscode-code-runner/issues/491)
The result should be something like this:
[](https://i.stack.imgur.com/eKm7H.png)
Am using angular CLI version 10, but angular cli v11 should yield the same results.


------------
    
    
## Answer \#2

 Vote: 1

Created at 2022-11-29 11:13:30

------------

Skip the test file with this command: 


------------
    
    