
# Post \#69505094 [Link](https://stackoverflow.com/questions/69505094/)

## Chain 2 queries in RTK Query

**Vote**: 11 (370/702) **Views**: 5287 (515/702) 

**Internal ID** \#0-0-29

Created at 2021-10-09 08:40:47

Tags: `javascript` `reactjs` `redux` `react-redux` `rtk-query`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

What is the proper way to chain queries if the 2nd query requires a parameter that is returned by the 1st?
```
const { data: user } = useGetUserQuery();
```

The user object contains an ID that is used to run
```
const { data: userBio} = useGetUserBioQuery(user.id);
```

How do I make sure the 2nd one runs only after the 1st one is fulfilled?


----------
        
## Answer \#0

**Accepted** Vote: 14

Created at 2021-10-09 09:29:40

------------

You can use the `skip` option:
```
const { data: user, isFulfilled: userFulfilled } = useGetUserQuery();
const { data: userBio} = useGetUserBioQuery(user.id, { skip: !userFulfilled });
```

Or a `skipToken`:
```
const { data: user, isFulfilled: userFulfilled } = useGetUserQuery();
const { data: userBio} = useGetUserBioQuery(userFulfilled ? user.id : skipToken);
```



------------
    
    
## Answer \#1

 Vote: 2

Created at 2022-01-17 15:17:46

------------

The usage section of the RTK Query docs also provides another example on how to combine multiple requests with one single query that I found very usefull for that use case:
[https://redux-toolkit.js.org/rtk-query/usage/customizing-queries#performing-multiple-requests-with-a-single-query](https://redux-toolkit.js.org/rtk-query/usage/customizing-queries#performing-multiple-requests-with-a-single-query)


------------
    
    
## Answer \#2

 Vote: 1

Created at 2023-01-09 15:48:58

------------

we can use `skip` flag to make sure if there is user id
```
const { data: user } = useGetUserQuery();
const { data: userBio} = useGetUserBioQuery(user.id, { skip: !user.id });
```



------------
    
    