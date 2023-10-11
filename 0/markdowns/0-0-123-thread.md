
# Post \#67884720 [Link](https://stackoverflow.com/questions/67884720/)

## Typescript: Omit nested property

**Vote**: 11 (370/702) **Views**: 8293 (454/702) 

**Internal ID** \#0-0-123

Created at 2021-06-08 09:11:42

Tags: `typescript`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I have an interface like this:
```
export interface Campaign {
  id: string
  orders?: number
  avgOrderValue?: number
  optionalAttributes: string[]
  attributeValues: {
    optionalAttributes: CampaignAttribute[]
    mandatoryAttributes: CampaignAttribute[]
    values?: { [key: string]: unknown }
  }
  created: number
  lastUpdated: number
}
```

And I want to create a type out of this for my form that needs to omit the `attributeValues.optionalAttributes` and `attributeValues.mandatoryAttributes` from the interface.
I was thinking that maybe Typescript can do something like this:
```
export type CampaignFormValues = Omit<Campaign, 'attributeValues.mandatoryAttributes'>
```

But this doesn't work.
I used the answer from this question: [Deep Omit with typescript](https://stackoverflow.com/questions/55539387/deep-omit-with-typescript)
But this answer just deep omits every matched key, so using it like this:
```
export type CampaignFormValues = Omit<Campaign, 'optionalAttributes'>
```

Would also remove the root level `optionalAttributes` which I want to keep.
Is there any way to do a nested omit with Typescript?


----------
        
## Answer \#0

**Accepted** Vote: 4

Created at 2021-06-08 13:35:46

------------

```
type A = {
    a: {
        b: string
        c: string
    }
    x: {
        y: number
        z: number,
        w: {
            u: number
        }
    }
}
type Primitives = string | number | boolean | symbol

/**
 * Get all valid nested pathes of object
 */
type AllProps<Obj, Cache extends Array<Primitives> = []> =
    Obj extends Primitives ? Cache : {
        [Prop in keyof Obj]:
        | [...Cache, Prop] // <------ it should be unionized with recursion call
        | AllProps<Obj[Prop], [...Cache, Prop]>
    }[keyof Obj]

type Head<T extends ReadonlyArray<any>> =
    T extends []
    ? never
    : T extends [infer Head]
    ? Head
    : T extends [infer Head, ...infer _]
    ? Head
    : never


type Tail<T extends ReadonlyArray<any>> =
    T extends []
    ? []
    : T extends [infer _]
    ? []
    : T extends [infer _, ...infer Rest]
    ? Rest
    : never

type Last<T extends ReadonlyArray<any>> = T['length'] extends 1 ? true : false


type OmitBase<Obj, Path extends ReadonlyArray<any>> =
    Last<Path> extends true
    ? {
        [Prop in Exclude<keyof Obj, Head<Path>>]: Obj[Prop]
    } : {
        [Prop in keyof Obj]: OmitBase<Obj[Prop], Tail<Path>>
    }

// we should allow only existing properties in right order
type OmitBy<Obj, Keys extends AllProps<Obj>> = OmitBase<A, Keys>

type Result = OmitBy<A,['a', 'b']> // ok

type Result2 = OmitBy<A,['b']> // expected error. order should be preserved
```

[Playground](https://www.typescriptlang.org/play?#code/C4TwDgpgBAglC8UDeAoK6oEMBcy0YICNcBnYAJwEsA7Ac3wPQGNSKb6CBfBqAD11SMMIXNQCuAW0IRyPAgC9Rk6eQA0cjAHcBGoWKVSZuqNy4puoSFAAKVCZWCUAbhBIIoZKnSgAfKOMNyXyhCAHtQgBsITGpgkhApSJQUAHoAKjS0NKgAcQhgLAiIqCdMCMoAE39XYAgqsExgAAtXKFCAMzbCACsIJmAslJRLaBgi21CwEgAeAHke1SgAYUwmFqgIXlrqCrcYcnJMEGnbSntHFxIAPncAbQBdG-geee6NrYgdt1Pz51aAfmWq3WOiE6FuEzAUBoUAA1hAQB0oK97thjH5bgA6bErNYQRaQ%205QFIpKDTAC0lKp0IKJCaoTEESq0igYmolFC7PkdSgmgcTSg5D6YnIJA5sSYZQi6Ng43Ikxmrwh8rA90WWJxwPxNhVjx4nFu8MRnRRyRGUAAEtEKtMACrvba7KAAJWtnIiIH2h2OMRAVyePHtm0dbgePEB1AgLlkBFwQY%20Xygtxo7Rklut93D6cwFR4cYdnydyeoqaCVpzi2xmJTaYA%20pmCIDy7nY9Vo8lhuBoLbMJQInaC4nXTn3Z6Dkdpr7-QhA4Oiw2MICw634yGkzWgvWs8uMPng4XQxuoLXK9ij66yAv0ICLwNW5H252rAAZTBkAf7odu6ger0TqdPFAtq3AA5FEdDNCBRKfk6ACMUCAhQYjQLg7RlCQEAduasznAAQm%20EBzAsNiNAKMFuMOFSjn%20PrUH6AYEK%2077WKRNzkVASGYY2eBgkmkLQrEACivBMBEYgVIRRpIq8izNicrFXKiyI9MqkxXiYUCgmCqlQjCUkmj0Sk4Q4%20EYUR3Q6WqQG9v2LHNP6%20pml2yJ4ccMlQAA0gibjsWMESQoqPTTogxnAKZhEwIsXkgNcTlWBejIFCFrnTJFoGYCBiwgYQUE3CSbSwnF0AJREwAAEzuKFuHHGl2W5cSpKbJA-Q8jI8rkJibTkBJQR0gyTIhNAYBChh5AuLmKBAA)
[More explanation](https://catchts.com/deep-pick) you can find in my blog
Above solution works with deep nested types
If you want to use dot syntax `prop1.prop2`, consider next type:
```
type Split<Str, Cache extends string[] = []> =
    Str extends `${infer Method}.${infer Rest}`
    ? Split<Rest, [...Cache, Method]>
    : Str extends `${infer Last}`
    ? [...Cache, Last,]
    : never
    
type WithDots = OmitBy<A, Split<'a.b'>> // ok
```



------------
    
    
## Answer \#1

 Vote: 5

Created at 2021-06-08 09:25:33

------------

First, Omit `attributeValues`, then add it back with properties removed.
```
export interface Campaign {
  id: string
  attributeValues: {
    optionalAttributes: string[]
    mandatoryAttributes: string[]
    values?: { [key: string]: unknown }
  }
}

type ChangeFields<T, R> = Omit<T, keyof R> & R;
type CampaignForm = ChangeFields<Campaign, { 
  attributeValues: Omit<Campaign['attributeValues'], 'mandatoryAttributes'|'optionalAttributes'> 
}>;

const form: CampaignForm = {
  id: '123',
  attributeValues: {
    values: { '1': 1 }
  }
}
```

[Playground](https://www.typescriptlang.org/play?ssl=21&ssc=1&pln=1&pc=1#code/KYDwDg9gTgLgBASwHY2FAZgQwMbDgYUwFsxMEBzJOAbwCg5EATALjgGcYplz65MZOCAEYBXVADVMAGxHA2rOgwYQwMBBCTSAggK6jU89oKTkA2gF1eDIpiSN+0AJ47B+uaw5cTFq3ABu0rJsAPwKcKYA1sCOHsbk5qwiSBFIEADuVAC+vNnZtDCOYHj4ABa25MAAYgjAUoxsADwAKgA0cABKAHxwALxwAPJECDDNbVGOEOgd3QBkHQDc+YXFxKQUSJXQRL0EZSZVNXWNhCRklG3UcLz8rmLAkjLuA0MjJ2uUpgDkN3p3D0GfcxtT42OwOKDOXTCO5sT4AH0+KjUGm0ULcsO6tEynUWtGwGg4cHQW1YbzOGy2O0UTFYnwAjAAmADMnxa1zRf0CT2pDACj0Ml3pn1YdLg2QYuVoQA)


------------
    
    
## Answer \#2

 Vote: 3

Created at 2021-06-08 09:21:44

------------

You need to create a new interface where `attributeValues` is overwritten:
```
export interface Campaign {
  id: string
  orders?: number
  avgOrderValue?: number
  optionalAttributes: string[]
  attributeValues: {
    optionalAttributes: CampaignAttribute[]
    mandatoryAttributes: CampaignAttribute[]
    values?: { [key: string]: unknown }
  }
  created: number
  lastUpdated: number
}

interface MyOtherCampaign extends Omit<Campaign, 'attributeValues'> {
    attributeValues: {
      values?: { [key: string]: unknown }
    }
}

let x:MyOtherCampaign;
```

[](https://i.stack.imgur.com/HsIws.png)
[Playground](https://www.typescriptlang.org/play?#code/JYOwLgpgTgZghgYwgAgMJwLYAc7AOYgCCYYUwARgK6TIDeAvgLABQLEAHlgPZRjKiRYiFOmy4CdFsn4ATAFzIAzqVB4pyHjOiKA-ApCUM5aOrgA3PAHkoWqADU4AG0oQ9yA0ZPNpXLGGBcIE7EKlSQigrKZCB4ANoAuqYkZGEQDs4QEZLe0hp%20AUGOISnUmQqiOPhEyRSlCerSGHAgMnBgPACexbXh5ZiVBN2p9TnSZk4uugq0yLEA1hAdkSox8QqUIHMgXADuIMhMOYfSCFAQbRDy7obGUOqOcMoAqlitkFcetyyHLALQ8EhkABZDqWMAAC2gFXE%20w4kBaimQlgwwDAAB5oVUADTIADkbVCpXSk1xAD5srlkASSpBiWUKblxhkpnRZgslkoVng1sgNltdvtjgdviwWI4IHx2HIQWDIVBMQQANwsIA)


------------
    
    