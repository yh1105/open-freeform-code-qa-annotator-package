
# Post \#52398449 [Link](https://stackoverflow.com/questions/52398449/)

## Error: Schema validation failed with the following errors: Data path "" should NOT have additional properties(project)

**Vote**: 92 (70/702) **Views**: 231892 (29/702) 

**Internal ID** \#0-0-34

Created at 2018-09-19 05:06:17

Tags: `angular` `typescript` `migration` `angular5` `angular6`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

After migrating application from angular 5 to 6, on running ng serve the following errors pop up.

> Schema validation failed with the following errors:
    Data path "" should NOT have additional properties(project).
  Error: Schema validation failed with the following errors:
    Data path "" should NOT have additional properties(project).
      at MergeMapSubscriber._registry.compile.pipe.operators_1.concatMap.validatorResult [as project] (.../TemplateApp/me-cmf-web-template-angular/node_modules/@angular-devkit/core/src/workspace/workspace.js:210:42)
      at MergeMapSubscriber._tryNext (/.../me-cmf-web-template-angular/node_modules/@angular-devkit/core/node_modules/rxjs/internal/operators/mergeMap.js:65:27)
      at MergeMapSubscriber._next (.../me-cmf-web-template-angular/node_modules/@angular-devkit/core/node_modules/rxjs/internal/operators/mergeMap.js:55:18)
      at MergeMapSubscriber.Subscriber.next (/home/training/Attinad_Projects/TemplateApp/me-cmf-web-template-angular/node_modules/@angular-devkit/core/node_modules/rxjs/internal/Subscriber.js:64:18)
      at MergeMapSubscriber.notifyNext (.../TemplateApp/me-cmf-web-template-angular/node_modules/@angular-devkit/core/node_modules/rxjs/internal/operators/mergeMap.js:84:26)
      at InnerSubscriber._next (.../me-cmf-web-template-angular/node_modules/@angular-devkit/core/node_modules/rxjs/internal/InnerSubscriber.js:25:21)
      at InnerSubscriber.Subscriber.next (/.../me-cmf-web-template-angular/node_modules/@angular-devkit/core/node_modules/rxjs/internal/Subscriber.js:64:18)
      at MapSubscriber._next (.../me-cmf-web-template-angular/node_modules/@angular-devkit/core/node_modules/rxjs/internal/operators/map.js:52:26)
      at MapSubscriber.Subscriber.next (/.../me-cmf-web-template-angular/node_modules/@angular-devkit/core/node_modules/rxjs/internal/Subscriber.js:64:18)
      at SwitchMapSubscriber.notifyNext (.../me-cmf-web-template-angular/node_modules/@angular-devkit/core/node_modules/rxjs/internal/operators/switchMap.js:77:26)

I assume that the error is with .angular.json file which I re-named from .angular-cli.json . 

My .angular.json file is as follows : 

```
{
    "$schema": "./node_modules/@angular/cli/lib/config/schema.json",
    "project": {
        "name": "mediaweb"
    },
    "apps": [{
            "root": "src",
            "outDir": "dist/browser",
            "assets": [
                "assets",
                "favicon.ico"
            ],
            "index": "index.html",
            "main": "main.ts",
            "polyfills": "polyfills.ts",
            "test": "test.ts",
            "tsconfig": "tsconfig.app.json",
            "testTsconfig": "tsconfig.spec.json",
            "prefix": "app",
            "styles": [
                "styles.scss",
                "../node_modules/owl.carousel/dist/assets/owl.carousel.css",
                "../node_modules/owl.carousel/dist/assets/owl.theme.default.css",
                "../node_modules/video.js/dist/video-js.css"
            ],
            "scripts": [
                "../node_modules/jquery/dist/jquery.js",
                "../node_modules/owl.carousel/dist/owl.carousel.js",
                "../node_modules/video.js/dist/ie8/videojs-ie8.js",
                "../node_modules/video.js/dist/video.js"
            ],
            "environmentSource": "environments/environment.ts",
            "environments": {
                "dev": "environments/environment.dev.ts",
                "prod": "environments/environment.prod.ts"
            }
        },
        {
            "platform": "server",
            "root": "src",
            "outDir": "dist/server",
            "assets": [
                "assets",
                "favicon.ico"
            ],
            "index": "index.html",
            "main": "main.server.ts",
            "test": "test.ts",
            "tsconfig": "tsconfig.server.json",
            "testTsconfig": "tsconfig.spec.json",
            "prefix": "app",
            "styles": [
                "styles.scss"
            ],
            "scripts": [],
            "environmentSource": "environments/environment.ts",
            "environments": {
                "dev": "environments/environment.dev.ts",
                "prod": "environments/environment.prod.ts"
            }
        }
    ],
    "e2e": {
        "protractor": {
            "config": "./protractor.conf.js"
        }
    },
    "lint": [{
            "project": "src/tsconfig.app.json",
            "exclude": ["**/node_modules/**", "**/UI/**"]
        },
        {
            "project": "src/tsconfig.spec.json",
            "exclude": "**/node_modules/**"
        },
        {
            "project": "e2e/tsconfig.e2e.json",
            "exclude": "**/node_modules/**"
        }
    ],
    "test": {
        "karma": {
            "config": "./karma.conf.js"
        }
    },
    "defaults": {
        "styleExt": "scss",
        "component": {}
    }
}
```


Should I re-structure the json file, if so how?.

Any help would be great.


----------
        
## Answer \#0

**Accepted** Vote: 7

Created at 2019-01-08 09:16:56

------------

Since I fixed the issue I thought it would be nice to post it here.

I changed my file name .angular.json to angular.json and replaced some properties from the question in it.

```
{
  "$schema": "./node_modules/@angular-devkit/core/src/workspace/workspace-schema.json",
  "version": 1,
  "newProjectRoot": "projects",
  "projects": {
    "template-appv6": {
      "root": "",
      "projectType": "application",
      "architect": {
        "build": {
          "builder": "@angular-devkit/build-angular:browser",
          "options": {
            "outputPath": "dist/browser",
            "index": "src/index.html",
            "main": "src/main.ts",
            "tsConfig": "src/tsconfig.app.json",
            "polyfills": "src/polyfills.ts",
            "assets": [
              {
                "glob": "**/*",
                "input": "src/assets",
                "output": "/assets"
              },
              {
                "glob": "favicon.ico",
                "input": "src",
                "output": "/"
              }
            ],
            "styles": [
              "src/styles.scss"
            ],
            "scripts": []
          },
          "configurations": {
            "production": {
              "optimization": true,
              "outputHashing": "all",
              "sourceMap": false,
              "extractCss": true,
              "namedChunks": false,
              "aot": true,
              "extractLicenses": true,
              "vendorChunk": false,
              "buildOptimizer": true,
              "fileReplacements": [
                {
                  "replace": "src/environments/environment.ts",
                  "with": "src/environments/environment.prod.ts"
                }
              ]
            }
          }
        },
        "serve": {
          "builder": "@angular-devkit/build-angular:dev-server",
          "options": {
            "browserTarget": "template-appv6:build"
          },
          "configurations": {
            "production": {
              "browserTarget": "template-appv6:build:production"
            }
          }
        },
        "extract-i18n": {
          "builder": "@angular-devkit/build-angular:extract-i18n",
          "options": {
            "browserTarget": "template-appv6:build"
          }
        },
        "test": {
          "builder": "@angular-devkit/build-angular:karma",
          "options": {
            "main": "src/test.ts",
            "karmaConfig": "./karma.conf.js",
            "polyfills": "src/polyfills.ts",
            "tsConfig": "src/tsconfig.spec.json",
            "scripts": [],
            "styles": [
              "src/styles.css"
            ],
            "assets": [
              {
                "glob": "**/*",
                "input": "src/assets",
                "output": "/assets"
              },
              {
                "glob": "favicon.ico",
                "input": "src",
                "output": "/"
              }
            ]
          }
        },
        "lint": {
          "builder": "@angular-devkit/build-angular:tslint",
          "options": {
            "tsConfig": [
              "src/tsconfig.app.json",
              "src/tsconfig.spec.json"
            ],
            "exclude": [
              "**/node_modules/**"
            ]
          }
        },
        "server": {
          "builder": "@angular-devkit/build-angular:server",
          "options": {
            "outputPath": "dist/server",
            "main": "src/main.server.ts",
            "tsConfig": "src/tsconfig.server.json"
          }
        }
      }
    },
    "template-appv6-e2e": {
      "root": "",
      "projectType": "application",
      "cli": {},
      "schematics": {},
      "architect": {
        "e2e": {
          "builder": "@angular-devkit/build-angular:protractor",
          "options": {
            "protractorConfig": "./protractor.conf.js",
            "devServerTarget": "template-appv6:serve"
          }
        },
        "lint": {
          "builder": "@angular-devkit/build-angular:tslint",
          "options": {
            "tsConfig": [
              "e2e/tsconfig.e2e.json"
            ],
            "exclude": [
              "**/node_modules/**"
            ]
          }
        }
      }
    }
  },
  "cli": {},
  "schematics": {
    "@schematics/angular:component": {
      "prefix": "app",
      "styleext": "css"
    },
    "@schematics/angular:directive": {
      "prefix": "app"
    }
  }
}
```


I changed the file by replacing the angular.json file with properties in the angular official docs.

The official [Angular Update Guide](https://update.angular.io/) provides a step by step guide on how to update versions.


------------
    
    
## Answer \#1

 Vote: 54

Created at 2019-06-07 10:54:14

------------

The issue is generally because of a version mismatch in your `package.json` file.
In my Angular 7 app, I have changed `"@angular-devkit/build-angular": "^0.800.2"` to `"@angular-devkit/build-angular": "~0.7.0"`.
Then I ran the command `npm install` after deleting `node_modules`.
Aside: Use `^` carefully. Your working code may not work in the future if there is a major change in the package used.
Using the same package-lock.json file will also do the magic.


------------
    
    
## Answer \#2

 Vote: 30

Created at 2019-07-03 07:51:24

------------

my app is on angular 7.2.3

remove  "es5BrowserSupport": true from angular.json.
and npm start now works.


------------
    
    
## Answer \#3

 Vote: 14

Created at 2020-02-19 14:39:03

------------

When i was getting version error issues,Following command worked for me:
First run:
```
npm update
```

Second run:
```
ng update
```

Third run:
(It will update all the mismatching packages)
```
npm update --all --force
```



------------
    
    
## Answer \#4

 Vote: 12

Created at 2019-08-13 08:13:43

------------

First:

```
npm uninstall @angular-devkit/build-angular
```


Next:

```
npm install @angular-devkit/build-angular@0.12.4
```



------------
    
    
## Answer \#5

 Vote: 10

Created at 2020-10-02 12:16:51

------------

Angular Upgrade from 9 to 10 is very easy (all additional detail-information is here: [https://update.angular.io/?v=9.0-10.0](https://update.angular.io/?v=9.0-10.0)) :
> Make sure your package file is in version control and all changes have been committed. This will overwrite your package file. Better you try it in a new experimental branch.
```
npm install -g npm-check-updates
ncu -u
ng update --all --force
```

In the new schema, it is no longer allowed to use the key `es5BrowserSupport`; remove the following line in your `angular.json`:
```
"es5BrowserSupport": true
```

then simply start your application anew:
```
npm start
```



------------
    
    
## Answer \#6

 Vote: 9

Created at 2021-12-14 10:50:11

------------

just go to the angular.json file and remove
> "extractCss": true
on the
> production section.


------------
    
    
## Answer \#7

 Vote: 6

Created at 2019-05-29 16:59:49

------------

Change `package.json` from:

```
"@angular-devkit/build-angular": "^0.13.5",
```


to:

```
"@angular-devkit/build-angular": "^0.12.1",
```



------------
    
    
## Answer \#8

 Vote: 3

Created at 2019-01-02 06:53:06

------------

the format of  needs to be as per Angular6... You can check my angular.json below or create a new project in Angular6 and then check the format of the 'project' head

```
{
  "$schema": "./node_modules/@angular/cli/lib/config/schema.json",
  "version": 1,
  "newProjectRoot": "projects",
  "projects": {
    "homePage6": {
      "root": "",
      "sourceRoot": "src",
      "projectType": "application",
      "prefix": "app",
      "schematics": {
        "@schematics/angular:component": {
          "styleext": "scss"
        }
      },
      "architect": {
        "build": {
          "builder": "@angular-devkit/build-angular:browser",
          "options": {
            "outputPath": "dist/homePage6",
            "index": "src/index.html",
            "main": "src/main.ts",
            "polyfills": "src/polyfills.ts",
            "tsConfig": "src/tsconfig.app.json",
            "assets": [
              "src/favicon.ico",
              "src/assets",
              "src/manifest.json"
            ],
            "styles": [
              "src/styles.scss"
            ],
            "scripts": []
          },
          "configurations": {
            "production": {
              "fileReplacements": [
                {
                  "replace": "src/environments/environment.ts",
                  "with": "src/environments/environment.prod.ts"
                }
              ],
              "optimization": true,
              "outputHashing": "all",
              "sourceMap": false,
              "extractCss": true,
              "namedChunks": false,
              "aot": true,
              "extractLicenses": true,
              "vendorChunk": false,
              "buildOptimizer": true,
              "serviceWorker": true
            }
          }
        },
        "serve": {
          "builder": "@angular-devkit/build-angular:dev-server",
          "options": {
            "browserTarget": "homePage6:build"
          },
          "configurations": {
            "production": {
              "browserTarget": "homePage6:build:production"
            }
          }
        },
        "extract-i18n": {
          "builder": "@angular-devkit/build-angular:extract-i18n",
          "options": {
            "browserTarget": "homePage6:build"
          }
        },
        "test": {
          "builder": "@angular-devkit/build-angular:karma",
          "options": {
            "main": "src/test.ts",
            "polyfills": "src/polyfills.ts",
            "tsConfig": "src/tsconfig.spec.json",
            "karmaConfig": "src/karma.conf.js",
            "styles": [
              "styles.scss"
            ],
            "scripts": [],
            "assets": [
              "src/favicon.ico",
              "src/assets",
              "src/manifest.json"
            ]
          }
        },
        "lint": {
          "builder": "@angular-devkit/build-angular:tslint",
          "options": {
            "tsConfig": [
              "src/tsconfig.app.json",
              "src/tsconfig.spec.json"
            ],
            "exclude": [
              "**/node_modules/**"
            ]
          }
        }
      }
    },
    "homePage6-e2e": {
      "root": "e2e/",
      "projectType": "application",
      "architect": {
        "e2e": {
          "builder": "@angular-devkit/build-angular:protractor",
          "options": {
            "protractorConfig": "e2e/protractor.conf.js",
            "devServerTarget": "homePage6:serve"
          }
        },
        "lint": {
          "builder": "@angular-devkit/build-angular:tslint",
          "options": {
            "tsConfig": "e2e/tsconfig.e2e.json",
            "exclude": [
              "**/node_modules/**"
            ]
          }
        }
      }
    }
  },
  "defaultProject": "homePage6"
}
```



I ran into similar (not identical, but also in angular.json) issue:

> Schema validation failed with the following errors:   Data path
  "['server']" should NOT have additional properties(scripts). Error:
  Schema validation failed with the following errors:   Data path
  "['server']" should NOT have additional properties(scripts).
      at MergeMapSubscriber._registry.compile.pipe.operators_1.concatMap.validatorResult
  [as project]
  (D:\Angular7\uHome\node_modules@angular-devkit\core\src\workspace\workspace.js:215:42)
      at MergeMapSubscriber._tryNext (D:\Angular7\uHome\node_modules\rxjs\internal\operators\mergeMap.js:69:27)
      at MergeMapSubscriber._next (D:\Angular7\uHome\node_modules\rxjs\internal\operators\mergeMap.js:59:18)
      at MergeMapSubscriber.Subscriber.next (D:\Angular7\uHome\node_modules\rxjs\internal\Subscriber.js:67:18)
      at MergeMapSubscriber.notifyNext (D:\Angular7\uHome\node_modules\rxjs\internal\operators\mergeMap.js:92:26)
      at InnerSubscriber._next (D:\Angular7\uHome\node_modules\rxjs\internal\InnerSubscriber.js:28:21)
      at InnerSubscriber.Subscriber.next (D:\Angular7\uHome\node_modules\rxjs\internal\Subscriber.js:67:18)
      at MapSubscriber._next (D:\Angular7\uHome\node_modules\rxjs\internal\operators\map.js:55:26)
      at MapSubscriber.Subscriber.next (D:\Angular7\uHome\node_modules\rxjs\internal\Subscriber.js:67:18)
      at SwitchMapSubscriber.notifyNext (D:\Angular7\uHome\node_modules\rxjs\internal\operators\switchMap.js:86:26)

had to remove the property scripts from my angular.json... sharing this information to help someone in the future.


------------
    
    
## Answer \#9

 Vote: 3

Created at 2020-10-04 14:52:44

------------

When upgrading `Angular` from version `8` to `10` I got very similar error, namely `Schema validation failed with the following errors: Data path "" should NOT have additional properties(serverTarget).`.
The solution was remove all `"serverTarget"` references from below `"serve"`. The below was :
```
"serve": {
  "builder": "@angular-devkit/build-angular:dev-server",
  "options": {
    "browserTarget": "my_app:build"
    "serverTarget": "my_app:server"
  },
  "configurations": {
    "production": {
      "browserTarget": "my_app:build:production",
      "serverTarget": "my_app:server:production"
    }
  }
},
```

The below is  and :
```
"serve": {
  "builder": "@angular-devkit/build-angular:dev-server",
  "options": {
    "browserTarget": "my_app:build"
  },
  "configurations": {
    "production": {
      "browserTarget": "my_app:build:production"
    }
  }
},
```

 for those who are working with `Angular Universal`. If you want to run SSR on `localhost`, then indeed you need the `serverTarget`. However, for that you have to create a new section within the `angular.json`, something like:
```
"serve-ssr": {
  "builder": "@nguniversal/builders:ssr-dev-server",
  "options": {
    "browserTarget": "my_app:build",
    "serverTarget": "my_app:server"
  },
  "configurations": {
    "production": {
      "browserTarget": "my_app:build:production",
      "serverTarget": "my_app:server:production"
    }
  }
},
```

Then `ng run my_app:serve-ssr`.
Actually I got this error when upgrading `Angular` from version `10` into `8` and wanted to improve some things. Then I've messed up the `serverTarget` thing with that wrong thinking, hopefully this will help out someone with `Angular Universal` in a project.


------------
    
    
## Answer \#10

 Vote: 2

Created at 2019-05-30 18:05:08

------------

Updating `npm` and `angular` fixed mine:

Assuming you use `npm` 

```
npm update
```


then run:

```
ng update
```


NB: You might be prompted to update individual dependencies. In my case, I was prompted to run:

`ng update @angular/cli` and `ng update @angular/core`.


------------
    
    
## Answer \#11

 Vote: 2

Created at 2021-10-04 04:44:44

------------

This error also shows when we add some properties that are not supported in the angular.json configuration
The Angular application we create must be listed under the `projects` properties  (with a 's'), but in this case it is under `project`
The top level properties are listed [here](https://angular.io/guide/workspace-config)


------------
    
    
## Answer \#12

 Vote: 1

Created at 2018-09-19 05:10:30

------------

I think it's with `RxJS`'s incompatibility. You should install `rxjs-compat` to fix this.

```
npm install rxjs-compat
```


Also, the name of the file is `angular.json` and not `.angular.json`


------------
    
    
## Answer \#13

 Vote: 1

Created at 2020-01-04 21:58:29

------------

go to `package.json` and change the version of  as follows:

`@angular-devkit/build-angular": "^0.12.4"`


------------
    
    
## Answer \#14

 Vote: 1

Created at 2020-11-06 16:19:32

------------

Remove these two lines from the angular.json file:
```
"vendorSourceMap":"true",
"evalSourceMap":"true".
```



------------
    
    
## Answer \#15

 Vote: 1

Created at 2021-07-02 08:20:11

------------


##### removing the flag es5BrowserSupport: true from angular.json fixed the problem.


Since I was updating from `Angular7` to `Angular11`, I skipped a point from [https://update.angular.io/](https://update.angular.io/) upgrade guide.

###### Remove any es5BrowserSupport flags in your angular.json and set your target to es2015 in your tsconfig.json. Angular now uses your browserslist to determine if an ES5 build is needed. ng update will migrate you automatically.




------------
    
    
## Answer \#16

 Vote: 0

Created at 2020-09-05 15:04:29

------------

I just deleted the  folder and run
> NPM Install
and it worked fine for me.
It is an  solution.


------------
    
    
## Answer \#17

 Vote: 0

Created at 2021-08-26 04:56:26

------------

After trying everything that are written above, it still didn't work.
Until, I rebooted my system.
Add this to your last resort.
- 


------------
    
    
## Answer \#18

 Vote: 0

Created at 2022-01-19 10:29:42

------------

just remove node_modules and do `npm i` sometimes some packages don't compile well, when you switch from one version to another version


------------
    
    
## Answer \#19

 Vote: 0

Created at 2022-02-10 00:57:29

------------

please check your "angular/cli" version in "package.json", the version number must be similar to other angular packages.
```
"devDependencies": {
    "@angular-devkit/build-angular": "~0.803.29",
     "@angular/cli": "~13.2.2", (this is the wrong one)
    "@angular/compiler-cli": "~8.2.14",
```



------------
    
    
## Answer \#20

 Vote: 0

Created at 2022-05-17 20:37:06

------------

In my project I was using a relative import in one of the files to another library outside the scope of ng-packagr (../another-lib). After changing to a module import (@scope/another-lib) it worked.


------------
    
    
## Answer \#21

 Vote: 0

Created at 2022-11-11 14:56:49

------------

I just faced this issue, and it was because I added "basehref" in the angular.json  section, which I think is not more supported, removed it, & it worked. & if anyone is troubled to add a root basehref into an angular app, you may need to do like below:-

1. import this <import {APP_BASE_HREF} from '@angular/common'> in app.module.ts
2. & then in the module array, add this as object into the list <{provide: APP_BASE_HREF, useValue: '/your-root-url/'}>




------------
    
    