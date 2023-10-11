
# Post \#66065099 [Link](https://stackoverflow.com/questions/66065099/)

## How to update GitHub authentification token on Rstudio to match the new policy?

**Vote**: 27 (213/702) **Views**: 11892 (395/702) 

**Internal ID** \#1-4-330

Created at 2021-02-05 14:31:56

Tags: `r` `git` `github` `rstudio` `access-token`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `r` (full parsed tag list: `r`)

----------

**Notepad**


----------

While pushing a commit to GitHub yesterday, I received an email from the GitHub team:
> Basic authentication using a password to Git is deprecated and will soon no longer work. Visit [https://github.blog/2020-12-15-token-authentication-requirements-for-git-operations/](https://github.blog/2020-12-15-token-authentication-requirements-for-git-operations/) for more information around suggested workarounds and removal dates.Thanks,
The GitHub Team
I don't understand a word of the help page...
I did create a more secure access token a few months ago when they first announced the change in authentification, and entered it in the terminal of my mac. I thought that would be it. My question is: 
Thanks in advance for your help,
Rosalie


----------
        
## Answer \#0

**Accepted** Vote: 46

Created at 2021-02-05 15:11:34

------------

I don't know how to validate user @r2evans' comment above, but indeed it worked!
I already had a PAT token set, so I followed the steps in [https://happygitwithr.com/credential-caching.html#credential-caching](https://happygitwithr.com/credential-caching.html#credential-caching), starting at 10.2.1.1
```
install.packages("gitcreds")
library(gitcreds)
gitcreds_set()
```

I had to enter my account password (computer password), then it showed my current identification parameters.
In the console, three choices appear:
```
1: Keep these credentials
2: Replace these credentials
3: See the password / token
```

Select 2 to replace the password/token (credentials), and voilà!
Thanks for the easy fix @r2evans.


------------
    
    
## Answer \#1

 Vote: 10

Created at 2021-04-27 12:50:34

------------

@r2evans this was very helpful!
For future visitors of this thread, running `gitcreds_set()` will prompt a dialog in Rstudio that will ask you what you would like to do with your previous existing user/password credentials.
```
1: Keep these credentials
2: Replace these credentials
3: See the password / token
```

By opting for 2, I replaced my credentials with an existing [token that I already created](https://docs.github.com/en/github/authenticating-to-github/creating-a-personal-access-token) on Github. It worked seamlessly!


------------
    
    
## Answer \#2

 Vote: 6

Created at 2022-02-08 09:06:12

------------

The solutions above didn't work for me. I had to use:
```
credentials::set_github_pat("my_pat")
```

Then when prompted by the pop-up boxes I had to use my PAT as my password.


------------
    
    