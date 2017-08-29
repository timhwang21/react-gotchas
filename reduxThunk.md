# Redux Thunk

## Don't abuse `setState()` in thunks

`setState()` is extremely powerful. Any action you dispatch now has access to the entire Redux store.

Unfortunately, the instant you use `setState()` you forfeit [referential transparency](https://wiki.haskell.org/Referential_transparency); that is to say, the output of your action is no longer entirely dependent on the inputs.

There are times where `setState()` is useful: for example, avoiding the need to connect a bunch of props that aren't used anywhere else, just so they can be passed to the action as arguments. But in this case, also consider if your action is possibly trying to do too much, and if it can be broken down into smaller pieces.

## Limit side-effects to thunks

Thunks are a sort of functional programming cheat. If an action sent a network request, it most certainly wouldn't be pure (or referentially transparent) any more, right? But if the action returned a function that carried out the network request, the action would still be pure (it returns the same function given the same inputs every single time).

This may seem like a semantic argument devoid of practicality -- after all, the side effect is still happening. But thunks provide a way to encapsulate side effects, such that their containers can be handled in a pure manner when working with React and Redux. By limiting side effects to thunks, you can be alerted to exactly where they are occurring in the codebase.

(A bit of clarity: the production of the thunk is pure. The thunk itself is not, and will cause a side effect when invoked.)