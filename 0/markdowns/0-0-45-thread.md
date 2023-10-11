
# Post \#69833591 [Link](https://stackoverflow.com/questions/69833591/)

## How to set the type for the state object in pinia?

**Vote**: 15 (318/702) **Views**: 18726 (313/702) 

**Internal ID** \#0-0-45

Created at 2021-11-04 01:34:17

Tags: `typescript` `vue.js` `vuejs3` `pinia`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I'm making a chess game and I am using Vue 3 and TypeScript with Pinia for the state management.
I want to do something like the following:
```
export const useStore = defineStore("game", {
  state: () => {
    return {
      moves: [],
      gameBoard:  getInitialBoard(),
      playerTurn: PieceColor.White,
      previousPieceSelected: undefined
    }
  },
    updatePreviousPieceSelected(piece: Piece | undefined ) {
      this.previousPieceSelected = piece
    }
  }
})
```

UpdateGameState.vue
```
setup() {
    const store = useStore()
    const previousPieceSelected: Piece | undefined = store.previousPieceSelected;
    let playerTurn: PieceColor = store.playerTurn;

    const initialGameState: GameState = {
      boardState: store.gameBoard,
      playerTurn,
    };

    const updateGameState = (
      cellRow: number,
      cellCol: number,
      currentPiece: Piece
    ) => {
      if (
        previousPieceSelected === undefined ||
        previousPieceSelected.pieceType === PieceType.None
      ) {
        store.updatePreviousPieceSelected(currentPiece);
      }
      if (
        (previousPieceSelected !== currentPiece && (currentPiece.pieceType === PieceType.None || currentPiece.color !== previousPieceSelected.color)) 
      ) {
        MovePiece(store.gameBoard, previousPieceSelected, {row: cellRow, col: cellCol} as Position)
        store.updatePreviousPieceSelected(undefined);
        store.changePlayer();
      }
    };
```

However, I get that an error on the following line:
```
store.updatePreviousPieceSelected(currentPiece);
```

That currentPiece (of type Piece) is not assignable to type undefined. I found a hack to get this to work by doing the following in my store:
```
export const useStore = defineStore("game", {
  state: () => {
    return {
      moves: [],
      gameBoard:  getInitialBoard(),
      playerTurn: PieceColor.White,
      previousPieceSelected: getInitialPreviousPieceSelected()
    }
  },
  actions: {
    changePlayer() {
      this.playerTurn =
          this.playerTurn === PieceColor.White
            ? PieceColor.Black
            : PieceColor.White;
    },
    updatePreviousPieceSelected(piece: Piece | undefined ) {
      this.previousPieceSelected = piece
    }
  }
})

function getInitialPreviousPieceSelected(): Piece | undefined {
  return undefined;
}
```

But it feels kluge. Is there another way to type previousPieceSelected in the initial state return?


----------
        
## Answer \#0

**Accepted** Vote: 26

Created at 2021-11-04 03:20:24

------------

The type of `this.previousPieceSelected` is inferred from the initial state, and it's currently initialized to `undefined`, so it thus has a type of `undefined` (meaning it can only be assigned a value of `undefined`).

1. Use type assertion on the initial undefined value (i.e., the as keyword plus the desired type of Piece | undefined).
2. Also note that optional parameters can be specified with ?: instead of | undefined. This is just a simpler way of writing it.


```
export const useStore = defineStore("game", {
  state: () => {
    return {
      moves: [],
      previousPieceSelected: undefined as Piece | undefined, 1️⃣
    }
  },
  actions: {                          2️⃣
    updatePreviousPieceSelected(piece ?: Piece) {
      this.previousPieceSelected = piece
    }
  }
})
```



------------
    
    
## Answer \#1

 Vote: 22

Created at 2022-02-17 21:18:01

------------

or just like this
```
interface IUserState {
  user: null | IUser
}
    
export const useUserStore = defineStore({
  id: 'user',
  state: (): IUserState => ({
    user: null,
  })
...
```



------------
    
    
## Answer \#2

 Vote: 6

Created at 2022-05-12 19:57:26

------------

For example:
```
type UserState = {
  login: string
  isPremium: boolean
}


const useUserStore = defineStore<string, UserState>('user', {
  state: () => ({
    login: 'test',
    isPremium: false,
  }),
})
```



------------
    
    
## Answer \#3

 Vote: 6

Created at 2022-05-18 09:23:08

------------

`defineStore` is the generic function in TypeScript that accepts two arguments, store id and state.
```
/**
 * Creates a `useStore` function that retrieves the store instance
 *
 * @param id - id of the store (must be unique)
 * @param options - options to define the store
 */
export declare function defineStore<Id extends string, S extends StateTree = {}, G extends _GettersTree<S> = {}, A = {}>(id: Id, options: Omit<DefineStoreOptions<Id, S, G, A>, 'id'>): StoreDefinition<Id, S, G, A>;
```

So it's possible to define fully-typed `State` and store name.
```
import { defineStore } from 'pinia';

interface State {
  borks: string[];
  isWoof: boolean;
}

interface Getters {}

interface Actions {
  addBork(bork: string) => void;
}

const useBorkStore = defineStore<'bork', State, Getters, Actions>('bork', {
  state: () => {
    return {
      borks: [],
      isWoof: false
    }
  },

  actions: {
    addBork(bork: string) {
      this.borks.push(bork);
    }
  }
})

export { useBorkStore };
```



------------
    
    
## Answer \#4

 Vote: 4

Created at 2022-11-22 03:31:59

------------

If the data is not loaded.
Try: [https://pinia.vuejs.org/core-concepts/state.html#typescript](https://pinia.vuejs.org/core-concepts/state.html#typescript)
other:
```
import {defineStore} from 'pinia'

interface Menu {
  path: string
  name: string
  component: string
  meta: {
    title: string
    icon: string
    affix?: string
  }
}

const useAppStore = defineStore('app', {
  state: () => ({
    state: <boolean>false, // boolean
    message： <string>'msg', // string
    tagsView: <Menu[]>[] // array
  })
})
export default useAppStore
```



------------
    
    
## Answer \#5

 Vote: 0

Created at 2022-04-22 02:58:59

------------

Where is `getInitialBoard()` coming from?
Is that an import?
your initialized data should not be your computed values. That is more the function of the "getters" in pinia. You have "state", "getters", and "actions". State is typically just the initialized, typed variables. Getters are computed returnable values without altering the state data. Actions change the state data. You should only change the state data with store actions, not from within the component instance outside of pinia. All the data, the source of truth, stays in pinia the whole time.
Keep your stores organized in that way:
```
// conceptual model e.g.
pinia {
  state: () => {
    return { data }
  },
  getters: { 
    // doesn't change data
    getter: (state) => state.data.returnComputedState(),
  },
  actions: { 
    // changes data
    action: () => this.data = mutateTheData(this.data),
  },
}
```



------------
    
    