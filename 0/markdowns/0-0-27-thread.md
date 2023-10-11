
# Post \#72083468 [Link](https://stackoverflow.com/questions/72083468/)

## 'loading' remains true when loading data with 'useQuery' using 'apolloClient' in react-native

**Vote**: 4 (532/702) **Views**: 3834 (549/702) 

**Internal ID** \#0-0-27

Created at 2022-05-02 07:18:54

Tags: `react-native` `apollo-client`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I'm trying to fetch data using Apollo Client `useQuery` in React Native, but `loading` hangs at true state and fails to fetch `data`.
below is the actual code.
`App.js`
```
import React from 'react';

import { SafeAreaView, Text, View } from 'react-native';

import { ApolloClient, ApolloProvider, InMemoryCache, gql, useQuery } from '@apollo/client';

const EXCHANGE_RATES = gql`
    query GetExchangeRates {
        rates(currency: "USD") {
            currency
            rate
        }
    }
`;

const TestScreen = () => {
    const { loading, error, data } = useQuery(EXCHANGE_RATES);

    console.log(loading, error, data); // Stop at "true undefined undefined" 

    if (loading) return <Text>Loading...</Text>;
    if (error) return <Text>Error :(</Text>;

    return data.rates.map(({ currency, rate }) => (
        <View key={currency}>
            <Text>
                {currency}: {rate}
            </Text>
        </View>
    ));
};

const apolloClient = new ApolloClient({
    // this uri is the uri provided by the apolloClient official documentation.
    // https://www.apollographql.com/docs/react/get-started#3-connect-your-client-to-react
    uri: 'https://48p1r2roz4.sse.codesandbox.io',
    cache: new InMemoryCache(),
});

const App = () => {
    return (
        <ApolloProvider client={apolloClient}>
            <SafeAreaView>
                <TestScreen />
            </SafeAreaView>
        </ApolloProvider>
    );
};

export default App;
```

`package.json`
```
{
    ...,
    "dependencies": {
        "@apollo/client": "^3.5.10",
        "graphql": "^16.4.0",
        "react": "17.0.2",
        "react-native": "0.68.1",
        "react-native-flipper-apollo-devtools": "^0.0.2"
    },
    "devDependencies": {
        "@babel/core": "^7.12.9",
        "@babel/runtime": "^7.12.5",
        "@graphql-codegen/cli": "^2.6.2",
        "@graphql-codegen/fragment-matcher": "^3.2.1",
        "@graphql-codegen/typed-document-node": "^2.2.8",
        "@graphql-codegen/typescript": "^2.4.8",
        "@graphql-codegen/typescript-apollo-client-helpers": "^2.1.15",
        "@graphql-codegen/typescript-operations": "^2.3.5",
        "@react-native-community/eslint-config": "^2.0.0",
        "@types/jest": "^26.0.23",
        "@types/react-native": "^0.67.3",
        "@types/react-test-renderer": "^17.0.1",
        "@typescript-eslint/eslint-plugin": "^5.17.0",
        "@typescript-eslint/parser": "^5.17.0",
        "babel-jest": "^26.6.3",
        "babel-plugin-module-resolver": "^4.1.0",
        "eslint": "^7.32.0",
        "eslint-config-prettier": "^8.5.0",
        "eslint-config-standard-with-typescript": "^21.0.1",
        "eslint-plugin-import": "^2.26.0",
        "eslint-plugin-node": "^11.1.0",
        "eslint-plugin-promise": "^6.0.0",
        "eslint-plugin-react": "^7.29.4",
        "eslint-plugin-react-hooks": "^4.5.0",
        "eslint-plugin-react-native": "^4.0.0",
        "husky": "^7.0.4",
        "jest": "^26.6.3",
        "lint-staged": "^12.4.1",
        "metro-react-native-babel-preset": "^0.67.0",
        "react-native-flipper": "^0.144.0",
        "react-test-renderer": "17.0.2",
        "typescript": "^4.4.4"
    },
    "resolutions": {
        "@types/react": "^17"
    },
    "jest": {
        "preset": "react-native",
        "moduleFileExtensions": [
            "ts",
            "tsx",
            "js",
            "jsx",
            "json",
            "node"
        ]
    }
}
```


After running `useQuery()`
```
loading = true, error = undefined, data = undefined
                    ↓
loading = false, error = undefined, data = { ... }
```


After running `useQuery()`
```
loading = true, error = undefined, data = undefined (Stuck at 'loading = true' )
```

I don't know what the problem is. Any help would be appreciated.


----------
        
## Answer \#0

**Accepted** Vote: 3

Created at 2022-05-07 19:15:59

------------

Version `3.6` implements `useQuery` using `useSyncExternalStore` API from React 18. Since expo currently supports React 17 you can solve the issue by downgrading `@apollo/client` to version `3.5.4` and `graphql` to version `^16.3.0` for now. You can find more information [here](https://github.com/apollographql/apollo-client/releases/tag/v3.6.0).


------------
    
    
## Answer \#1

 Vote: 3

Created at 2022-05-09 18:34:33

------------

Unfortunately, this is a known bug, which happened in apollo client 3.6. The last proper working version was 3.5.10. After that, useQuery and useLazyQuery stopped working (stuck on loading), but only on mobile devices/emulators. The web version still working properly.
Try to downgrade to 3.5.10.
[https://github.com/apollographql/apollo-client/issues/9689](https://github.com/apollographql/apollo-client/issues/9689)
EDIT: The new alpha version solves that case (3.7.0-beta.3).
Try : `npm i @apollo/client@beta`


------------
    
    
## Answer \#2

 Vote: 1

Created at 2022-05-11 23:17:13

------------

Upgrading to the new alpha version (3.7.0-beta.3)
from @Dominik Myszkowski fixed my issue, downgrading to 3.5.4 did not.


------------
    
    
## Answer \#3

 Vote: 0

Created at 2023-02-14 21:58:39

------------

What fixed it for me was downgrading `styled-react-modal` from `^2.1.0` to `^1.2.3`.
Not sure why, except that it uses a provider that's near the top of the component tree.


------------
    
    