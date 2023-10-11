
# Post \#71626538 [Link](https://stackoverflow.com/questions/71626538/)

## Defining multiple mapped types in typescript?

**Vote**: 7 (449/702) **Views**: 967 (660/702) 

**Internal ID** \#0-0-48

Created at 2022-03-26 08:43:22

Tags: `typescript` `types`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

Say I have the following typescript code:
```
type fruit = "apple" | "banana" | "pear"
type color = "red" | "yellow" | "green"
```

And I want to create a type that has a numeric property for each fruit and a boolean property for each color, something like
```
type FruitsAndColors = {
  [key in fruit]: number;
  [key in color]: boolean
}
```

Unfortunately, this errors with the message "A mapped type may not declare properties or methods", yet it compiles fine. What's actually happening here?
I can get around this with something like
```
type FruitsAndColors = {
  [key in fruit]: number;
} & {
  [key in color]: boolean
}
```

But I'd like to know what the actual issue is.


----------
        
## Answer \#0

**Accepted** Vote: 5

Created at 2022-03-26 08:51:56

------------

It's not "vscode's typescript extension," it's . It doesn't let you do two mappings in a single `type` construct. It's just the syntax of the construct doesn't allow it.
Instead, you do what you showed:
```
type FruitsAndColors = {
    [key in fruit]: number;
} & {
    [key in color]: boolean
};
```

Note, though, that that type  to be present in the object. Maybe that's what you want, but if not, add `?` after the mapped keys (or wrap the entire thing in `Partial<>`):
```
type FruitsAndColors = {
    [key in fruit]?: number;
} & {
    [key in color]?: boolean
};
// Or
type FruitsAndColors = Partial<{
    [key in fruit]: number;
} & {
    [key in color]: boolean
}>;
```

[Playground for the above](https://www.typescriptlang.org/play?ssl=32&ssc=13&pln=32&pc=29#code/C4TwDgpgBAZgTgVwJbCgXigIgIZjAGwkygB8sAjbAO2u2LM0mzkwG4AoUSKAYwHt8fOOixwIAE3pYQEfIIDuUzAHMxEKm3adw0AGKIUAZwCCVcQGEBQwwEYRAb3ZRnUANoBrCCChIqsA8AAugBcUFQIALbkEHAcAL5QAGRQji5unt6+vFZwIVDkfAIQ1OxxHFx6ASZmloJwhgBMIgAKzMBI2PgAPKkuHl4+fvDIQaHhUTHxSSlOfRmD2XV5BUUlcQB8HOwA9NtQuthI+Ib5EDzYCIbQfDBQEUiGhr7KUGBwfJBw7RCG7PxUhlQMGwPBsoX0I2qFhytgcs2cuAIEFCNgANPCoGJxKFgIgIOiylpdlAAOpCdwnaLnS7QYAACy8AHIxFBOvgoPSYhA-nwAUCQQ1wVVTNC6rCML0EXhCCj0WlKDQaKEGnKXEw4KEAMyq5xYnF4nVQGRyPjyULA474jGqCDqfUIK2EnZ7MlwCmnalXDkM17vT7fE7Ma5gdq8zo8vmwEGaoWQkW1axNCUYxEyqBojF6jkG0ocIA)


------------
    
    
## Answer \#1

 Vote: 1

Created at 2022-03-26 09:02:58

------------

Typescript syntax for [mapped types](https://www.typescriptlang.org/docs/handbook/2/mapped-types.html) doesn't allow multiple mappings. There are two ways you can achieve what you want:

1. You can use a type union, like the one in your question.
2. You can also use Record<Fruit, number> & Record<Color, boolean>.


As mentioned in the other answer, note that this will make all properties required, so wrap it all in `Partial` if that's not what you want.
[Playground](https://www.typescriptlang.org/play?#code/C4TwDgpgBAYgTgVwJbCgXigIgIZjAGwkygB8sAjbAO2u2LM0mzkwFgAoUSKAYQHt8fOOixwIAE3pYQEfIIDuUzAHMxEKm3Ycu0AB7xkwAM4BBKuP6C4RkQG8OUKAG0A1hBBQkVWIhQBdAC4oKgQAW3IIOABuB2c3Dy9eASFAqHI+AQhqKKgAelyocT4IIyoAclR5IRcOAF8ObXBoEzkDFFNzSyEbDAAlCABjIXEAHjbgABpgsIi4AD4oADIofqG4Ua64KfTM6jmYziafQw6LZOsRAAVmYCRsfBHV4bHfSenwyIXlp-WRze2MoQ9vsGuxCKhsEFxqdNj0oPZ2I5HGJxEFgIgIHUokA)
Also note that types should start with a capital letter. This is a TS convention that helps with syntax highlighting.


------------
    
    
## Answer \#2

 Vote: 1

Created at 2022-03-26 09:04:17

------------

You can also use conditional types:
```
type FruitsAndColors = Partial<{
  [Key in (Fruit | Color)]: Key extends Fruit ? number : Key extends Color ? boolean : never;
}>
```

[Playground](https://www.typescriptlang.org/play?#code/C4TwDgpgBAYgTgVwJbCgXigIgIZjAGwkygB8sAjbAO2u2LM0mzkwChRIoBhAe3x7joscCABN6WEBHz8A7hMwBzERCptW7cNHjJgAZwCCVUb35w9QgArNgSbPgA8Ab1ZQoAbQDSEEFCRUoAAodFFJuPgEASgBdAC4ob18IAA9gVVELENQAfigqBABbcghBeMSoFLTjC1MBKFzyHj4Iaih4qggANxKAblYAXwA%20DQBjHio9VB5yACsIEeB4rMNjWvMhFzdcAgh4gEYAGlcoEVF4gDN7PQgj-qA)


------------
    
    