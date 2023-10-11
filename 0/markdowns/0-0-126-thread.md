
# Post \#74216515 [Link](https://stackoverflow.com/questions/74216515/)

## Next.js 13 - Error: The "target" property is no longer supported in next.config.js, Even next.config.js does not have the target property

**Vote**: 4 (532/702) **Views**: 3055 (567/702) 

**Internal ID** \#0-0-126

Created at 2022-10-27 03:35:39

Tags: `next.js`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I upgraded my next.js app to Next.js 13 and pushed the new version to AWS Amplify. The build failed due to this error: The "target" property is no longer supported in next.config.js
`Error: The "target" property is no longer supported in next.config.js. See more info here https://nextjs.org/docs/messages/deprecated-target-config at Object.loadConfig [as default (/codebuild/output/src405507991/src/assistian/node_modules/next/dist/server/config.js:97:19)`
Here is my next.config.js with no target:
```
/** @type {import('next').NextConfig} */
module.exports = {
  webpack(config) {
    config.module.rules.push({
      test: /\.svg$/i,
      issuer: /\.[jt]sx?$/,
      use: ['@svgr/webpack'],
    })
    return config
  }
}
```

Any opinion on what is going wrong?


----------
        
## Answer \#0

**Accepted** Vote: 2

Created at 2022-10-27 09:53:28

------------

Appears to be unsupported at this time as per [https://github.com/vercel/next.js/issues/41932](https://github.com/vercel/next.js/issues/41932)


------------
    
    
## Answer \#1

 Vote: 2

Created at 2023-01-06 12:08:36

------------

This worked for me [https://docs.aws.amazon.com/amplify/latest/userguide/update-app-nextjs-version.html](https://docs.aws.amazon.com/amplify/latest/userguide/update-app-nextjs-version.html), need to migrate to Amplify Hosting compute for NextJS 13 (I use SSR). My Amplify was created before release of Amplify Hosting compute (~ November 2022)


------------
    
    
## Answer \#2

 Vote: 1

Created at 2022-12-02 15:40:52

------------

Amplify currently supports NextJS versions greater than version 11, via a migration. See here: [https://docs.aws.amazon.com/amplify/latest/userguide/update-app-nextjs-version.html](https://docs.aws.amazon.com/amplify/latest/userguide/update-app-nextjs-version.html)


------------
    
    