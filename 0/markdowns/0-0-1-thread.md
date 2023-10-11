
# Post \#65771294 [Link](https://stackoverflow.com/questions/65771294/)

## NextJS deployed to Vercel: 404 page not found

**Vote**: 21 (256/702) **Views**: 26296 (252/702) 

**Internal ID** \#0-0-1

Created at 2021-01-18 08:50:00

Tags: `next.js` `vercel`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I have a NextJS app without an integrated api that I want to deploy to Vercel. It works fine when I run it locally `yarn run dev` and I can also build it `yarn run build` without any errors. When I deploy it Vercel, however, I receive a `404 Error`.
Here is my folder structure:
```
app/
  - components
  - pages
    - editor
      - [id].tsx
    ...tsx
  - public
  - utils
    - db
      api.ts
```

I am using NextJs 10.0.3.
Here is a link to the deployed [app](https://clubhouse-bio-2qgg4rvij.vercel.app).
I don't have a nextjs config file. My assumption is that the error is caused by the dynamic route but I can't find my mistake. Also, no page is working when the app is deployed as opposed to only the dynamic page.
Do you have pointers on where to look next?
EDIT 1:
[Here](https://github.com/JanRuettinger/ClubhouseBio) is a link to the GitHub repo.
EDIT 2:
I found an issue but don't know how to fix it.
This is how my build output looks like on Vercel:
[](https://i.stack.imgur.com/Vfw3p.png)
This is how it's supposed to look like on Vercel:
[](https://i.stack.imgur.com/HKeVr.png)
Not sure why the whole `_next` folder is missing.


----------
        
## Answer \#0

**Accepted** Vote: 21

Created at 2021-01-20 20:53:26

------------

I just had this exact same issue.
I have my Next app in a sub directory. I think that might be the cause of some problems.
What worked for me was.
- - 


------------
    
    
## Answer \#1

 Vote: 21

Created at 2022-05-14 07:46:33

------------

Go to Project Settings
Change Framework preset from `Other` to `Next.js`
Redeploy the project.


------------
    
    
## Answer \#2

 Vote: 3

Created at 2021-01-18 09:03:28

------------

Edited
Try to deploy using
`now --prod --force`
The `--force` flag will clear your build cache (in production) and force production push.
If still no working then make sure to add now.json


------------
    
    
## Answer \#3

 Vote: 2

Created at 2022-01-07 16:46:26

------------

Also make sure to choose Next.js as Framework preset (and not other). You can find that in Project settings and under Build & Development Settings.
More here: [https://vercel.com/docs/concepts/deployments/build-step](https://vercel.com/docs/concepts/deployments/build-step)


------------
    
    
## Answer \#4

 Vote: 0

Created at 2021-08-30 04:54:23

------------

My issue was that I first attempted to deploy Vercel through a team. When I created a new Vercel project under my individual Vercel account and deployed the same code, it worked! I hope this saves someone else time because I just lost 3 hours trying to find this. I'm using Next.js as well.
If you are trying to deploy through a team, you might need to configure the team ID in your vercel.json first e.g.
```
{
  "currentTeam": "team_ofwUZockJlL53hINUGCc1ONW"
}
```

ref: [https://vercel.com/docs/configuration#global/config-json/current-team](https://vercel.com/docs/configuration#global/config-json/current-team)


------------
    
    