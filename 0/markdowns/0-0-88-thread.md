
# Post \#57124269 [Link](https://stackoverflow.com/questions/57124269/)

## RangeError: invalid array length

**Vote**: 5 (499/702) **Views**: 25467 (259/702) 

**Internal ID** \#0-0-88

Created at 2019-07-20 11:26:34

Tags: `javascript` `arrays` `reactjs`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I want to get an array out of the keys on my object but the length should 0 when the Object is empty. The length of array is correct when I try `console.log()` with the array but my code stucks throwing the following error on my browser and it stops the execution:

```
RangeError: invalid array length
burger/transformedIngredients<
src/components/Burger/Burger.js:8

   5 | const burger = (props) => {
   6 |   let transformedIngredients = Object.keys(props.ingredients).map(igKey => (
   7 |     // eslint-disable-next-line max-len,react/no-array-index-key
>  8 |     [...Array(props.ingredients[igKey])].map((_, i) => <BurgerIngredient key={igKey + i} type={igKey} />)
   9 |   )).reduce((arr, el) => (
  10 |     arr.concat(el)
  11 |   ), []);
```


This is the code I'm using:

```
const burger = (props) => {
  let transformedIngredients = Object.keys(props.ingredients).map(igKey => (
    // eslint-disable-next-line max-len,react/no-array-index-key
    [...Array(props.ingredients[igKey])].map((_, i) => <BurgerIngredient key={igKey + i} type={igKey} />)
  )).reduce((arr, el) => (
    arr.concat(el)
  ), []);

  if (transformedIngredients.length === 0) {
    transformedIngredients = <p>Please add some ingredients!</p>;
  }
```


I am passing `ingredients` from here:

```
class BurgerBuilder extends Component {
  state = {
    ingredients: {
      salad: 0,
      bacon: 0,
      cheese: 0,
      meat: 0,
    },
    totalPrice: 4,
  };
render() {
    return (
      <Fragment>
        <Burger ingredients={this.state.ingredients} />
        <BuildControls
          ingredientAdded={this.addIngredientHandler}
          ingredientRemoved={this.removeIngredientHandler}
        />
      </Fragment>
    );
  }
```



----------
        
## Answer \#0

**Accepted** Vote: 2

Created at 2019-07-20 11:38:14

------------

I don't really understand why are you doing an extra mapping of the keys and creating a multidimensional array, but you can change your code to this if you want to render an ingredient for each key: 

```
const burger = (props) => {
  let transformedIngredients = Object.keys(props.ingredients).map((igKey, i) => (
    <BurgerIngredient key={igKey + i} type={igKey} />
  ));
}
```



------------
    
    
## Answer \#1

 Vote: 3

Created at 2019-07-20 11:47:59

------------

The error may happen if one of ingredients has a negative value, for example:

```
state = {
  ingredients: {
    salad: -1, // this will cause the error
    bacon: 0,
    cheese: 0,
    meat: 0,
  },
  totalPrice: 4,
};
```


You can prevent this situation by ensuring that negative number will not be passed in the `Array` constructor, for example you can pass `0` if the number is negative:

```
[...Array(Math.max(0, props.ingredients[igKey]))]
```



------------
    
    
## Answer \#2

 Vote: 3

Created at 2019-07-24 14:02:38

------------

I'm taking the same course and got the same error. Check your reducer.js file and compare it to source code provided in the course. In my case, the array passed from reducer.js didn't match the array received by Burger.js. The error was gone after fixing the reducer.js file.

My code looks like this after the fix:

```
const reducer = (state = initialState, action) => {
  switch (action.type) {
    case actionTypes.ADD_INGREDIENT:
      return {
        ...state,
        ingredients: {
          ...state.ingredients,
          [action.ingredientName]: state.ingredients[action.ingredientName] + 1
        },
        totalPrice: state.totalPrice + INGREDIENT_PRICES[action.ingredientName]
      };
    case actionTypes.REMOVE_INGREDIENT:
      return {
        ...state,
        ingredients: {
          ...state.ingredients,
          [action.ingredientName]: state.ingredients[action.ingredientName] - 1
        },
        totalPrice: state.totalPrice + INGREDIENT_PRICES[action.ingredientName]
      };
    default:
      return state;
    }
    };
```



------------
    
    
## Answer \#3

 Vote: 2

Created at 2019-10-15 18:45:23

------------

I am learning react from same course and i had same issue that you have. In your const INGREDIENT_PRICES all object should be same case as you are using in buildcontrols.js and in this const controls check uppper and lower cause of words match with INGREDIENT_PRICES then issue will be resolved.


------------
    
    
## Answer \#4

 Vote: 1

Created at 2020-01-01 10:58:04

------------

in this case i narrowed down my error to when trying to pass the price value to the query params by setting Number.parseFloat and toFixed on the price integer, thus eliminating the error causing stringified decimal.

```
purchaseContinueHandler = () => {            
            const queryParams = [];
            let price = Number.parseFloat(this.state.totalPrice).toFixed(2);

            for (let i in this.state.ingredients) {
                queryParams.push(encodeURIComponent(i) + '=' + encodeURIComponent(this.state.ingredients[i]));
            }
            queryParams.push('price=' + price);

            const queryString = queryParams.join('&'); 

            this.props.history.push({
                pathname: '/checkout',
                search: '?' + queryString
            });
```


the error was caused for me by having a string decimal in the price value. 


------------
    
    
## Answer \#5

 Vote: 1

Created at 2020-10-02 17:07:14

------------

I was following the same course and faced same issue. Included `totalPrice` as key value pair in ingredients object(in reducer.js file) which is not the case as it is separate key value pair in updated state for each action.
Before fix-
```
import * as actionTypes from './actions';

const initialState = {
    ingredients: {
        salad: 0,
        bacon: 0,
        cheese: 0,
        meat: 0
    },
    totalPrice: 4,
};

const INGREDIENT_PRICES = {
    salad: 0.5,
    bacon: 1.3,
    cheese: 0.6,
    meat: 1,
};

const reducer = (state = initialState, action) => {
    switch(action.type){
        case actionTypes.ADD_INGREDIENT: return {
            ...state,
            ingredients: {
                ...state.ingredients,
                [action.ingredientName]: state.ingredients[action.ingredientName] + 1,
                `totalPrice: state.totalPrice + INGREDIENT_PRICES[action.ingredientName]`
            }
        };

        case actionTypes.REMOVE_INGREDIENT: return {
            ...state,
            ingredients: {
                ...state.ingredients,
                [action.ingredientName]: state.ingredients[action.ingredientName] - 1,
                `totalPrice: state.totalPrice - INGREDIENT_PRICES[action.ingredientName]`
            }
        }

        default : return state;
    }
};

export default reducer;
```

After fix-
```
import * as actionTypes from './actions';

const initialState = {
    ingredients: {
        salad: 0,
        bacon: 0,
        cheese: 0,
        meat: 0
    },
    totalPrice: 4,
};

const INGREDIENT_PRICES = {
    salad: 0.5,
    bacon: 1.3,
    cheese: 0.6,
    meat: 1,
};

const reducer = (state = initialState, action) => {
    switch(action.type){
        case actionTypes.ADD_INGREDIENT: return {
            ...state,
            ingredients: {
                ...state.ingredients,
                [action.ingredientName]: state.ingredients[action.ingredientName] + 1,
            },
            `totalPrice: state.totalPrice + INGREDIENT_PRICES[action.ingredientName]`
        };

        case actionTypes.REMOVE_INGREDIENT: return {
            ...state,
            ingredients: {
                ...state.ingredients,
                [action.ingredientName]: state.ingredients[action.ingredientName] - 1,
            },
            `totalPrice: state.totalPrice - INGREDIENT_PRICES[action.ingredientName]`
        }

        default : return state;
    }
};

export default reducer;
```



------------
    
    
## Answer \#6

 Vote: 0

Created at 2020-02-04 06:54:02

------------

Has the same error as yours, possible error was in the adding of ingredients.

Solved mine by changing "[]" to "{}"  in the line of copying object. I did not noticed that I used the [].


------------
    
    
## Answer \#7

 Vote: 0

Created at 2020-04-30 08:12:10

------------

I made the same mistake too, changing 'b' and 'c' to uppercase 
in "bacon" and "cheese". 

By making every character in the same case the issue is resolved:

```
const controls=[
  {label:'salad',type:'salad'},
  {label:'bacon',type:'bacon'},
  {label:'cheese',type:'cheese'},
  {label:'meat',type:'meat'}
  ];
```



------------
    
    
## Answer \#8

 Vote: 0

Created at 2020-06-03 10:42:38

------------

I am following the same course please check the ingredient name you are using in your database those should be same as in the `BuildControls.js`, ingredients type should be same as you write in your database. This will definitely solve your problem. 

This error occurs because of the selection of wrong name that's why it says Invalid array length.


------------
    
    
## Answer \#9

 Vote: 0

Created at 2020-07-20 11:45:12

------------

Thanks to all of you that have taken the time to provide your answer and thoughts.
I had the same issue and the solution was just to change the key value to lowercase,
For example on the code below:
The mistake was having `'Price'` Uppercased instead of `'price'` lowercased
```
checkoutContinuedHandler = () => {
        const queryParams = [];
        for (let ing in this.state.ingredients) {
            queryParams.push(encodeURIComponent(ing) + '=' + encodeURIComponent(this.state.ingredients[ing]));
        }
        queryParams.push('price=' + this.state.totalPrice.toFixed(2));
        const queryString = queryParams.join('&');
        this.props.history.push({
            pathname: '/checkout',
            search: '?' + queryString
        });
    }
```




------------
    
    
## Answer \#10

 Vote: 0

Created at 2021-01-13 07:02:59

------------

Just remove the spread Operator from ...state.ingredients and it will Work!


------------
    
    