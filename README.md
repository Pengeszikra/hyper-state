# typerdux
> Under development!
react state handling helper based on useReducer and typescript
> maybe the final name will be something else

This minimal library will help to organize mid complex state handling under with type guarded dispatch capable actions.

## install

```sh
pnpm add typerdux
```

## example

> Declare the set of actions with their types
```ts
// actions.ts

export enum actions {
  START_APPLICATION = "START_APPLICATION",
  PLACE_CONTENT = "PLACE_CONTENT",
  ADD_ITEM = "ADD_ITEM",
}

export type ActionTypes =
  | { type: actions.START_APPLICATION, payload: {start: number, id: string } }
  | { type: actions.PLACE_CONENT, payload: number[] }
  | { type: actions.ADD_ITEM, payload: number };
```

> Example of use in saga
```ts
// exampleGenerator.ts

import { typedPutActionMapFactory } from 'typerdux';
import { actions, ActionTypes } from './actions;


const run = typedPutActionMapFactory<typeof actions, AppActions>(actions);

export function * exampleGenerator() {
  yield run.START_APPLICATION({
    start: Date.now(), 
    id: Math.random().toString(32).slice(-8)
  });
  yield run.PLACE_CONENT([87, 45, 23, 12]);
  yield run.ADD_ITEM(42);
}
```

## Fun-Functions test 

```tsx
import { FC, Reducer, useEffect, useMemo } from "react";
import { typedActionMapFactory } from "typerdux";
// @ts-ignore
import { Saga } from "redux-saga/effects";
import { gameSaga } from "../definitions/gameSaga";
import { GameActions, GameState, game, initialTable } from "../definitions/formulaTroll";
import { gameReducer } from "../definitions/gameReducer";
import useSagaReducer from "use-saga-reducer";
import { CardView } from "../components/CardView";


export const Multiplayer: FC = () => {

  const [_state_, dispatch] = useSagaReducer<Saga<GameActions>, Reducer<GameState, GameActions>, GameState>(gameSaga, gameReducer, initialTable);
  const actions = useMemo(() => typedActionMapFactory<typeof game, GameActions>(game, dispatch), [dispatch]);
  const state = _state_ as GameState;

  useEffect(() => {
    console.log('-- effect by actions --');
    actions.LETS_PLAY(true);
  }, [actions]);

  return (
    <main className="bg-black text-white  min-h-screen grid place-items-center relative">
      <section className="m-4">

        <section className={`min-w-screen grid grid-cols-2 gap-4`}>
        {state.players.map(
          (({deck, id}) => (
            <section className="w-[200px] grid gap-4" key={id}>
              {deck.map((card) => <CardView {...card} key={id+card.id} />)}
            </section>
          ))
        )}
        </section>
      </section>
      {/* <code>{JSON.stringify(state?.grand?.[1], null, 2)}</code> */}
    </main>
  );
}
```

## Behind the scene

... short description about implementation

## Why important use redux-reduce - without redux

... I can show `typerdux` capability by with a simple game

## npm package create with vite: 
[creating-a-typescript-library-with-vite](https://onderonur.netlify.app/blog/creating-a-typescript-library-with-vite/)

## npm local test

https://dev.to/scooperdev/use-npm-pack-to-test-your-packages-locally-486e

```sh
npm pack --pack-destination ~
```