
# Post \#72361047 [Link](https://stackoverflow.com/questions/72361047/)

## Error: No "exports" main defined in graphql-upload/package.json

**Vote**: 7 (449/702) **Views**: 7364 (478/702) 

**Internal ID** \#0-0-102

Created at 2022-05-24 10:10:12

Tags: `javascript` `graphql` `nestjs` `express-graphql`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

Have installed graphql-upload, do
`import { graphqlUploadExpress } from 'graphql-upload';`
And getting this error:
Error: No "exports" main defined in graphql-upload/package.json
Dependencies:
```
"graphql-upload": "^14.0.0",
"graphql": "15.8.0",
"graphql-request": "^4.2.0",
"graphql-tools": "^8.2.0",
"@nestjs/axios": "^0.0.7",
"@nestjs/common": "^8.4.1",
"@nestjs/config": "^1.1.5",
"@nestjs/core": "^8.4.1",
"@nestjs/graphql": "^9.1.2",
"@nestjs/platform-express": "^8.0.0",
```

The version of node: v16.10.0


----------
        
## Answer \#0

**Accepted** Vote: 1

Created at 2022-05-25 11:06:31

------------

So the problem was in the .default build settings.
You can remove it, but when we removed it we saw the problem with other modules, so we resolved this issue via this:
```
import Upload = require('graphql-upload/Upload.js');
```

It looks very dirty, but it works.
You can check conversation about this module in issues on GitHub: [https://github.com/jaydenseric/graphql-upload/issues/305#issuecomment-1136574019](https://github.com/jaydenseric/graphql-upload/issues/305#issuecomment-1136574019)


------------
    
    
## Answer \#1

 Vote: 4

Created at 2022-05-25 08:42:07

------------

I have personally put a ts-ignore for ignore errors.
```
// @ts-ignore
import GraphQLUpload from 'graphql-upload/GraphQLUpload.js';
// @ts-ignore
import Upload from 'graphql-upload/Upload.js';
```

And imports like that. I hope it can help even if it's dirty!


------------
    
    
## Answer \#2

 Vote: 3

Created at 2022-05-24 10:27:56

------------

`graphql-upload` library doesn't have any main `index.js` re-export for all of its functions. It has direct file exports for all the specific functionalities. It is specified in its `package.json` file under `exports` key like so:
```
"exports": {
    "./GraphQLUpload.js": "./GraphQLUpload.js",
    "./graphqlUploadExpress.js": "./graphqlUploadExpress.js",
    "./graphqlUploadKoa.js": "./graphqlUploadKoa.js",
    "./package.json": "./package.json",
    "./processRequest.js": "./processRequest.js",
    "./Upload.js": "./Upload.js"
  },
```

So instead of directly importing from package root you need to specify a sub-module path like this:
`import graphqlUploadKoa from "graphql-upload/graphqlUploadKoa.js";`
Reference: [package.json of graphql-upload](https://github.com/jaydenseric/graphql-upload/blob/master/package.json)


------------
    
    
## Answer \#3

 Vote: 1

Created at 2022-06-17 14:44:16

------------

The owner of the package just chooses to restrict importing everything from the index file, so to run the new version (> 13.0.0) you have to change the way you are importing the package:
```
const graphqlUploadExpress = require('graphql-upload/graphqlUploadExpress.js');
```

or
```
const GraphQLUpload = require('graphql-upload/GraphQLUpload.js');
```

Alternatively, you can download grade your package to version 13.0.0


------------
    
    
## Answer \#4

 Vote: 1

Created at 2022-07-29 02:57:38

------------

Just like every answer, the maintainer of the package has chosen to restrict the imports for this package. I run into the same issue and have decided to stick with version 13 moving forward.
You can use the old imports with version 13
```
import { graphqlUploadExpress } from 'graphql-upload';
```



------------
    
    
## Answer \#5

 Vote: 1

Created at 2022-08-01 18:30:14

------------

Just ran into this problem.
Apparently, the the new version `i.e. ^16` ,has a major [update](https://github.com/jaydenseric/graphql-upload/releases/tag/v16.0.0)
now you need to do
`const Upload = require('graphql-upload/Upload.mjs');`
or
`import { default as Upload } from 'graphql-upload/Upload.mjs';`
Instead of `.js`, all the imports needs to be from `.mjs`.
Hope this helps!


------------
    
    
## Answer \#6

 Vote: 0

Created at 2022-09-13 16:58:58

------------

use version v15.0.1 for graphql-upload
import like this:
import GraphQLUpload from 'graphql-upload/GraphQLUpload.js';
import graphqlUploadExpress from 'graphql-upload/graphqlUploadExpress.js';
[https://github.com/jaydenseric/graphql-upload/issues/314#issuecomment-1140441744](https://github.com/jaydenseric/graphql-upload/issues/314#issuecomment-1140441744)
[https://github.com/jaydenseric/graphql-upload/releases/tag/v15.0.1](https://github.com/jaydenseric/graphql-upload/releases/tag/v15.0.1)
Supported runtime environments:
Node.js versions ^14.17.0 || ^16.0.0 || >= 18.0.0.
- Projects must configure TypeScript to use types from the ECMAScript modules that have a // @ts-check comment:- compilerOptions.allowJs should be true.- compilerOptions.maxNodeModuleJsDepth should be reasonably large, e.g. 10.- compilerOptions.module should be "node16" or "nodenext".
[https://www.npmjs.com/package/graphql-upload](https://www.npmjs.com/package/graphql-upload)


------------
    
    
## Answer \#7

 Vote: 0

Created at 2023-01-28 15:09:19

------------

I faced the same issue and I fixed it using an alternate package [graphql-upload-minimal](https://www.npmjs.com/package/graphql-upload-minimal)
Here is the old code
```
const {
  graphqlUploadExpress, // A Koa implementation is also exported.
} = require("graphql-upload");
const { GraphQLUpload } = require("graphql-upload");
```

Here is the new code
```
const {
  graphqlUploadExpress, // A Koa implementation is also exported.
} = require("graphql-upload-minimal");
const { GraphQLUpload } = require("graphql-upload-minimal");
```

This fixed it.


------------
    
    
## Answer \#8

 Vote: 0

Created at 2023-02-26 00:10:06

------------

I had the same problem.
I fixed using version 15.0.2 (only that version works for me)
> package.json
```
{
   "dependencies": {
      "@nestjs/apollo": "^10.1.7",
      "@nestjs/axios": "1.0.0",
      "@nestjs/common": "^9.3.9",
      "@nestjs/config": "^2.0.0",
      "@nestjs/core": "^9.3.9",
      "@nestjs/graphql": "10.2.0",
      "@nestjs/platform-express": "^9.3.9",
      "graphql": "^16.6.0",
      "graphql-upload": "15.0.2"
   },
   "exports": {
     "./GraphQLUpload.js": "./GraphQLUpload.js",
     "./graphqlUploadExpress.js": "./graphqlUploadExpress.js",
     "./graphqlUploadKoa.js": "./graphqlUploadKoa.js",
     "./package.json": "./package.json",
     "./processRequest.js": "./processRequest.js",
     "./Upload.js": "./Upload.js"
   }
}
```

> tsconfig.json
```
{
  "compilerOptions": {
    "module": "commonjs",
    "declaration": true,
    "removeComments": true,
    "emitDecoratorMetadata": true,
    "experimentalDecorators": true,
    "allowSyntheticDefaultImports": true,
    "target": "es2017",
    "sourceMap": true,
    "outDir": "./dist",
    "baseUrl": "./",
    "incremental": true,
    "allowJs": true,
    "maxNodeModuleJsDepth": 10
  }
}
```

> **/resolver.js
```
// @ts-ignore
    import Upload = require('graphql-upload/Upload.js');
    // @ts-ignore
    import GraphQLUpload = require('graphql-upload/GraphQLUpload.js');
    
    ...
    
    @Mutation(() => Boolean, {
       name: 'uploadImages',
       description: 'Insert array photos',
    })
    async uploadImages(
        @Args('files', { type: () => [GraphQLUpload] })
        files: [Upload],
        @Args('metadata')
        metadata: UploadImagesMetadataArgs,
      ): Promise<Boolean> {
        const functionPrefix = 'uploadImages';
        try {
          let uploadImagesArgs: Array<UploadImagesArgs> = [];
          for (const file of files) {
            // @ts-ignore
            const { filename, mimetype, encoding, createReadStream } = await file;
const stream = createReadStream();
            const chunks = [];
            for await (const chunk of stream) {
               chunks.push(chunk);
            }
            const buffer = Buffer.concat(chunks);
            uploadImagesArgs.push({ buffer, filename, mimetype });
            // your code with connect with services for save your images
            return true;
          }
        } catch(error){
             // your code 
             return false;
        }
      }
```



------------
    
    