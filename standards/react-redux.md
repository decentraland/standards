# Decentraland's React/Redux Guidelines

**Document Status**: _Draft_.  
**Maintainer**: Juan Cazala &lt;[juan@decentraland.org](mailto:juan@decentraland.org)&gt;.

## Introduction

Coding style and conventions for our React/Redux apps

## Table of Content

1. [Directory structure](https://github.com/decentraland/standards/blob/master/standards/react-redux.md#1-directory-structure)
2. [Naming conventions and file structure](https://github.com/decentraland/standards/blob/master/standards/react-redux.md#2-naming-conventions-and-file-structure)
3. [Performance](https://github.com/decentraland/standards/blob/master/standards/react-redux.md#3-performance)
4. [Component Types](https://github.com/decentraland/standards/blob/master/standards/react-redux.md#4-component-types)
5. [Imports order on files](https://github.com/decentraland/standards/blob/master/standards/react-redux.md#5-imports-order-on-files)
6. [Bonus tracks](https://github.com/decentraland/standards/blob/master/standards/react-redux.md#5-bonus-tracks)

## 1. Directory structure

The sources of the project follow this structure:

```
/src
  /modules
    /{domain}
      /actions.ts
      /actions.spec.ts
      /reducer.ts
      /reducer.spec.ts
      /selectos.ts
      /selectos.spec.ts
      /sagas.ts
      /sagas.spec.ts
      /types.ts
      /{subdomain}
  /components
    /{component}
      /{component}.tsx
      /{component}.spec.ts
      /{component}.container.ts
      /{components}.types.ts
      /{component}.css
      /index.ts
      /{subcomponent}
```

There are two main branches, `/modules` and `/components`.

## /modules

This directory contains all the redux modules of the application.

It follows a domain driven structure, where each domain of the application will be represented as a directory, and all the actions (action types, action creators, and thunks/sagas), reducers and selectors (and all their test specs) for that specific domain will live inside.

A domain may contain child domains or subdomains (domains that are only used by or relevant to their parent domain). These will follow the same structure as their parent.

The strutures of the application's redux state tree and the `/modules` directory tree must be very similar if done right. This will make debugging more intuitive and onboarding new developers easier.

### Why?!

Usually when a bug is found, or a refactor is needed, it involves all the redux parts in a domain: changing an action may impact on the reducer, that may also impact on the selectors if the shape of the state is affected, thus requiring updates on the test specs for the three of them.

Keeping all the parts together makes refactoring/fixing/updating code easier. Also, at some point some part of a domain may need to be shared accross projects (ie, the User domain might be shared between apps that use the same authentication system), it is very straight forward to extract a domain as a separate package when all the parts (and their dependencies) live under the same directory.

## /components

This directory contains all the React components.

Each component has it's own directory, with a component file, a styles file, and its typings and tests. It ALWAYS has an `index.ts` that exports the component. A component may also have a `.container.ts` file which basically consist of the `mapState` and `mapDispatch` functions, and returns the connected HOC.

A component can have child components or subcomponents (components that are only used by or relevant to their parent component). These will follow the same structure as their parent.

### Why?!

Why don't we have a separate `/components` and `/containers` folders like everyone else?

Why do we need that annoying `index.ts` file??

As the application grows, components grow with them. Sometimes a component needs to be split into smaller components with different responsabilities. When this happens sometimes a container is better off as a regular component with child containers instead:

For example, take this components:

```jsx
<Container>
  <ComponentA />
  <ComponentB />
  <ComponentC />
</Container>
```

Say `<Container>` is now mapping +20 props, that passes down to the three children components, that also pass them down to their children components.

Having so many props and following them down the component tree can make the code difficult to debug and also makes refactors more painful, so it might be smarter to connect the child components in this case and leave the parent and a regular component:

```jsx
<Component>
  <ContainerA />
  <ContainerB />
  <ContainerC />
</Component>
```

Now these three containers have a smaller mapped surface than the +20 props parent, and props are not passed downsseveral steps.

This is also more performant since now a change in the store might trigger a re-render of a single container, instead of re-rendering the only parent container with its three children.

Now, since each component has a `.container.ts` file we can just copy the parent's one to each of the children's directory, and strip out the unnecesary props.

Now is when that `index.ts` file pays off, on the children components we just point it from the component file, to the `.container.ts` file, and we do the oposite on the parent component. And everything keeps working as before, but we have cleaner, more performant code. Easy refactor.

This helps the application to stay healthy and scalable as it grows.

Having the `.container.ts` separated from the component itself makes the component easier to test (we just need to test the un-connected component, no need to mock a redux store). And also makes it easy to mock data and develop components when the redux modules that it uses are not ready yet (you just mock the `mapState` and `mapDispatch` results).

## 2. Naming conventions and file structure

### 2.1 Components

**Naming conventions:**

- **callback props**: start with `on` + the name of the event, ie: `onMouseMove`, `onOpen`, `onChange`.

- **boolean props**: start with `is` or `has`, ie: `isLoading`, `hasError`.

- **handlers**: start with `handle` + the name of the event, ie: `handleMouseMove`, `handleOpen`, `handleChange`.

**File structure:**

Each file contains a single component, which's name matches the name of the file, (always in PascalCase) and it is exported as `default`, ie:

```ts
import React from 'react'
...
export class MyComponet extends React.PureComponent {
  render() {
    // ...
  }
}
```

### 2.2 Containers

Containers have no jsx, they just import the component they will connect, they have two functions `mapState` and `mapDispatch` (optionally `mergeProps` if needed), and finally they export the HOC as `default`, ie:

```js
import { connect } from 'react-redux'
import Component from './Component'

const mapState = state => {
  // ...
}

const mapDispatch = dispatch => {
  // ...
}

export default connect(
  mapState,
  mapDispatch
)(Component)
```

### 2.3 Reducer

**File structure:**

1. Start with an `INITIAL_STATE`

2. Export a function `{domain}Reducer`

**Example:**

```js
import { SOMETHING, SomethingAction } from './actions'

export type SomethingState = {
  something: number
}

const INITIAL_STATE = {
  something: 1
}

export type SomethingReducerAction = SomethingAction // | OtherAction | AnotherAction

export function somethingReducer(state: SomethingState = INITIAL_STATE, action: SomethingReducerAction): SomethingState {
  switch(action.type) {
    case SOMETHIG:
      return {
        ...state,
        something: action.value
      }
    case default:
      return state
  }
}
```

### 2.4 Actions

Export all action types as constants at the beginning, then all the action creators as functions

Example:

```ts
import { action } from 'typesafe-actions'

// Do Something

export const DO_SOMETHING = 'Do Something'

export const doSomething = (someParam: string) =>
  action(DO_SOMETHING, {
    someParam
  })

export type DoSomethingAction = ReturnType<typeof doSomething>
```

For async actions types, use the prefix `_REQUEST`, `_SUCCESS` and `_FAILURE`, and for action creators `Request`, `Success` and `Failure`.

```ts
import { action } from 'typesafe-actions'
import { Something } from './types'

// Fetch Something

export const FETCH_SOMETHING_REQUEST = '[Request] Fetch Something'
export const FETCH_SOMETHING_SUCCESS = '[Success] Fetch Something'
export const FETCH_SOMETHING_FAILURE = '[Failure] Fetch Something'

export const fetchSomethingRequest = (someParam: string) =>
  action(FETCH_SOMETHING_REQUEST, {
    someParam
  })

export const fetchSomethingSuccess = (something: Something) =>
  action(FETCH_SOMETHING_SUCCESS, {
    something
  })

export const fetchSomethingFailure = (errorMessage: string) =>
  action(FETCH_SOMETHING_FAILURE, {
    errorMessage
  })

export type FetchSomethingRequestAction = ReturnType<
  typeof fetchSomethingRequest
>
export type FetchSomethingSuccessAction = ReturnType<
  typeof fetchSomethingSuccess
>
export type FetchSomethingFailureAction = ReturnType<
  typeof fetchSomethingFailure
>
```

### 2.5 Sagas

export a `function*` named `{domain}Saga`, then place all the handlers below.

```js
import {
  FETCH_SOMETHING_REQUEST,
  FETCH_SOMETHING_SUCCESS,
  FETCH_SOMETHING_FAILURE
} from './actions'

export function* somethingSaga() {
  yield takeLatest(FETCH_SOMETHING_REQUEST, handleFetchSomething)
}

function* handleFetchSomething(action) {
  try {
    const something = yield call(() => fetch(...))
    yield put(fetchSomethingSuccess(something))
  } catch (error) {
    yield put(fetchSomethingFailure(error.message))
  }
}
```

The pattern that we use to trigger actions from a different domain is always `take`'ing the action, never `put`'ing it.

**BAD:**

```ts
// modules/wallet/sagas.ts
import { fetchSomethingRequest } from 'modules/something/actions'

export function* saga() {
  yield takeLatest(LOGIN_REQUEST, handleLoginRequest)
}

function* handleLoginRequest(action) {
  try {
    ...
    yield put(loginSuccess(...))
    yield put(fetchSomethingRequest())
  } catch (error) {
    yield put(loginFailure(error.message))
  }
}
```

**GOOD:**

```ts
// modules/something/sagas.ts
import {
  CONNECT_WALLET_SUCCESS,
  ConnectWalletSuccessAction
} from 'modules/wallet/actions'
import { fetchSomethingRequest } from './actions'

export function* saga() {
  yield takeLatest(CONNECT_WALLET_SUCCESS, handleConnectWalletSuccess)
}

function* handleConnectWalletSuccess(action: ConnectWalletSuccessAction) {
  yield put(fetchSomethingRequest())
}
```

Always use action creators when you use `put`, do not create plain action objects inside a saga:

**BAD:**

```ts
import { FETCH_SOMETHING_REQUEST } from 'modules/something/actions'
// ...
yield put({ type: FETCH_SOMETHING_REQUEST })
```

**GOOD:**

```ts
import { fetchSomethingRequest } from 'modules/something/actions'
// ...
yield put(fetchSomethingRequest())
```

### 2.6 Selectors

For selector we use lambdas, we use `get` as prefix unless the return value is a boolean, in that case we use `is`/`has`. Unless the state is too simple, add a `getState` selector that returns the root of the state of this domain, and let the other selectors depend on it (so it's easier to refactor the shape of the application state later).

**Example:**

```ts
export const getState = state => state.something
export const getSomething = state => getState(state).data
export const isLoading = state => getState(state).loading
export const getError = state => getState(state).error
```

### 2.7 Types

The domain specific types will live in a `types.ts` file:

```ts
export type Something = {
  // ..
}
```

**Example:**

```ts
export const getState = state => state.something
export const getSomething = state => getState(state).data
export const isLoading = state => getState(state).loading
export const getError = state => getState(state).error
```

## 3. Performance

### 3.1 Use `PureComponent`s

We should use `PureComponent`s for all our components, except when they depend on the `context`. They work in the same way as regular `Component`s but they implement a `shouldComponentUpdate()` with a shallow prop and state comparison. This can save tons of unnecessary renders.

i.e:

```jsx
const Stateless = () => {
  console.log('stateless')
  return <p>Stateless</p>
}

class Pure extends React.PureComponent {
  render() {
    console.log('pure')
    return <p>Pure</p>
  }
}
```

You will notice that the `Stateless` component triggers its render function on every keystroke while the pure doesn't:

![pure-stateless](https://user-images.githubusercontent.com/2781777/34625919-51923b08-f239-11e7-8f75-2ea120a3aded.gif)

### 3.2 Avoid creating stuff inside the `render()` function

The `render()` function is one of the most called functions, so we should try to minimize the memory footprint by not creating objects or functions. Also, since we use `PureComponent`s if we create new objects, that will trigger unnecessary renders in the components down the tree because it will make the shallow props comparison to fail.

**BAD:**

```jsx
class Options extends React.PureComponent {
  render() {
    const options = ['A', 'B', 'C']
    return <select options={options}>
  }
}
```

**GOOD:**

```jsx
const options = ['A', 'B', 'C']

class Options extends React.PureComponent {
  render() {
    return <select options={options}>
  }
}
```

Same thing applies for functions:

**BAD:**

```jsx
class Input extends React.PureComponent {
  ...
  render() {
    return <input value={this.state.value} onChange={e => this.setState({ value: e.target.value }}>
  }
}
```

**GOOD:**

```jsx
class Input extends React.PureComponent {
  ...
  handleChange = e => this.setState({ value: e.target.value }}
  render() {
    return <input value={this.state.value} onChange={this.handleChange}>
  }
}
```

### 3.3 Use memoized selectors

Using memoized selectors (like `reselect`) helps preventing unnecessary computation and since the
reference is memoized (it returns the same reference), when we combine this with
`PureComponent` that will also save tons of unnecessary renders.

**BAD:**

```js
export const getList = state => {
  const books = getBooks(state)
  const authors = getAuthors(state)
  return Object.keys(books).map(bookId => {
    const book = books[bookId]
    return {
      ...book,
      author: authors[book.authorId]
    }
  })
}
```

**GOOD:**

```js
export const getList = createSelector(getBooks, getAuthors, (books, authors) =>
  Object.keys(books).map(bookId => {
    const book = books[bookId]
    return {
      ...book,
      author: authors[book.authorId]
    }
  })
)
```

## 4. Component Types

Each component must have a `{components}.types.ts` with the following structure:

```tsx
// Component

export type DefaultProps = {
  title: ''
  width: number
  height: number
  onClick: () => void
}

export type Props = Partial<DefaultProps> & {
  id: string // this is a required prop
}

export type State = {
  // this might not be needed if the component doesn't have internal state
}

export type Context = {
  // this might not be needed if the component doesn't consume the context
}

// Container
export type StateProps = Pick<Props, 'title' | 'width' | 'height'>
export type DispatchProps = Pick<Props, 'onClick'>
```

### Usage

**Component**

```tsx
import * as React from 'react'
import { Props, DefaultProps, State, Context } from './types'

export default class MyComponent extends React.PureComponent<Props, State> {
  static defaultProps: DefaultProps {
    width: 600,
    height: 400
  }

  context: Context // only if needed

  render() {
    const { title, width, height, onClick } = this.props
    return (
      <div style={{ width, height }} onClick={onClick}>{title}</div>
    )
  }
}
```

**Container**

```tsx
import MyComponent from './MyComponent'
import { getThing } from 'modules/thing/selectors'
import { Props, MapStateProps, MapDispatchProps } from './MyComponent.types'
import { RootState, RootDispatch } from 'types'

export const mapState = (state: RootState, { id }: Props): MapStateProps => {
  const thing = getThing(state, id)
  return {
    title: thing.title,
    width: thing.width,
    height: thing.height
  }
}

export const mapDispatch = (
  dispatch: RootDispatch,
  { id }: Props
): MapDispatchProps => ({
  onClick: () => console.log(`you clicked ${id}`)
})

export default connect(
  mapState,
  mapDispatch
)(Component)
```

**App**

```tsx
import * as React from 'react'
import MyComponent from 'components/MyComponent'

export class App extends React.PureComponent {
  render() {
    return <MyComponent id={5} />
  }
}
```

### Why?!

Notice that all the properties in `DefaultProps` are mandatory, and this is intended, so if we add a prop to the component that is optional, that means that it **MUST** have a default value. So once we add a new property to the `DefaultProps` type, TypeScript is not going to let us compile until we go to the component and add a default value to it.

Making all the props required in the `DefaultProps` is not an issue in the final `Props` type because we extend `Partial<IDefaultProps>` which turns them all into optional properties, so the final type will only mark as required the properties extended by `Props`. If the component doesn't have any default props we can just define `Props`.

To type the `mapState` and `mapDispatch` functions in the container we want to export types created by using `Pick`. In some cases (when ALL the component's props come from the state) the `MapStateProps` will match `Props`. In this case we can reuse `Props` instead of creating `MapStateProps`. Also some components don't map any prop to `dispatch`, in those cases there's no point in exporting `DispatchProps`.

To type the internal state and the context we will export types named `State` and `Context`.

### 4.1 Types and interfaces

Use `interface` when you want to describe **what an object can do**. The whole purpose of interfaces is to tell what capabilities a class has, that's why an object can implement several interfaces:

```ts
interface IReadable {
  read(): string
}

interface IWritable {
  write(value: string): void
}

class Store implements IReadable, IWritable {
  constructor(public value: string) {}

  read() {
    return this.value
  }

  write(value: string) {
    this.value = value
  }
}

// now Store instances can be consumed by classes that expect IReadable or IWritable objects

class User {
  constructor(public name: string) {}
  save(store: IWritable) {
    store.write(this.name)
  }
}
```

When you need to describe **what an object is** without defining implementation (or partially) use abstract classes:

```ts
abstract class Component {

  constructor(public props: any) {}

  setState(state) {
    this.state = state
    this.render()
  }

  abstract render(): any
}

class MyComponent extends Component {
  render() {
    return <p onClick={() => this.setState({ clicked: true })}>
  }
}
```

Use `type` when you want to describe _the shape of an object_. Most of the typing (if not all) in a react-redux application fall under this case:

```ts
// modules/user/types.ts

type User = {
  id: number
  name: string
}
```

```ts
import { User } from 'modules/user/types'

type SaveUserOptions = {
  // options
}

function saveUser(user: User, options?: SaveUserOptions) {
  // save user
}
```

Use PascalCase for types and a "I" preffix for interfaces:

```tsx
export type User = {
  id: number
  name: string
}

export interface IUserService {
  users: User[]
}
```

### Why?

Types and interfaces are very similar, we decided to use types for shapes and interfaces for objects contracts since interfaces are the ones that should conceptually be implemented. Interface in TypeScript can have the same name as the implementation, we need the "I" preffix so we can differ one from another:

**BAD**

```tsx
interface Foo {
  bar: () => void
}

export class Foo implements Foo {
  bar() {
    // stuff
  }
}
```

**GOOD**

```tsx
interface IFoo {
  bar: () => void
}

export class Foo implements IFoo {
  bar() {
    // stuff
  }
}
```

## 5. Imports order on files

Imports should be grouped together in the following sets of imports:

1. System and third-party imports, like `fs`, `react` and `express`.
2. First-party libraries, like `decentraland-ui`
3. Local imports, starting with imports referenced as modules and then sorted by distance

- Use local absolute paths when importing root components, and use relative paths when importing child components
- Newlines are mandatory
- Comments are not mandatory

```javascript
/* global libraries */
import React from 'react'
/* first-party libraries */
import ui from 'decentraland-ui'

/**
 * local imports, on each section, keep the NODE_PATHs first, then less specific first.
 * For example: `modules/actions` comes first, next `./XX`, `../XX`, `../../XX`,
 */
/* actions imports */
import actions from 'modules/actions'
/* selectors imports */
import selectors from 'modules/selectors'
/* sagas imports */
import sagas from 'modules/sagas'
/* utils & helpers imports */
import util from 'utils'
/* components imports */
import MyComponent from 'components/MyComponent'

/* css imports */
import './MyComponent.css'
```

**BAD:**

```javascript
import utils from './utils'
import React from 'react'
import 'MyCss.css'
```

**GOOD:**

```javascript
import React from 'react'

import utils from './utils'

import 'MyCss.css'
```

**BAD:**

```javascript
import React from 'react'

import utils from 'utils'
import Component from './MyComponent'
import sagaA from 'modules/moduleA/sagas'
import actions from 'modules/module/actions/actionsA'
import sagaB from 'modules/moduleB/sagas'
import selectors from 'modules/moduleB/selectors'

import 'MyCss.css'
```

**GOOD:**

```javascript
import React from 'react'

import actions from 'modules/module/actions/actionsA'
import selectors from 'modules/moduleB/selectors'
import sagaA from 'modules/moduleA/sagas'
import sagaB from 'modules/moduleB/sagas'
import utils from 'utils'
import Component from './MyComponent'

import 'MyCss.css'
```

**BAD:**

```javascript
import React from 'react'

import utils from '../utils'
import helpers from 'helpers'
import a from '../a'
import b from 'folder/b'
import c from '../../c'

import 'MyCss.css'
```

**GOOD:**

```javascript
import React from 'react'

import helpers from 'helpers'
import b from 'folder/b'
import utils from './utils'
import a from '../a'
import c from '../../c'

import 'MyCss.css'
```

**BAD:**

```javascript
import React from 'react'

import rootComponent from '../rootComponent'
import childComponent from 'components/currentFolder/childComponent'

import 'MyCss.css'
```

**GOOD:**

```javascript
import React from 'react'

import rootComponent from 'components/rootComponent'
import childComponent from './childComponent'

import 'MyCss.css'
```

### Why?!

Keeping a consistent order helps us quickly scan imports and know what's going on without having to know exactly what we're looking for.

### Good to have

We can configure it as a [linter rule](https://eslint.org/docs/rules/sort-imports)

## 6. Bonus tracks

### Avoid exporting stuff as `default`

Avoid using `export default`, except for Components and Containers.

**BAD**

```js
// Foo.js
const Foo = 5
export default Foo
```

**GOOD**

```js
// Foo.js
export const Foo = 5
```

### Why?!

By avoiding it we get many benefits when working on VSCode, Atom or other code editors, since every exported property is a well defined named export:

1. Automatic imports, because they are well defined named exports

```js
const foo = new Foo() // hit enter and auto-imports Foo!
```

2. Auto complete and suggestions

```js
import { /* get suggestions/autocompletion here */
```

3. Prevent typos:

With `export default`:

```js
import Foo from 'foo'
import foo from 'foo'
```

Without `export default`:

```js
import { Foo } from 'foo'
```

4. Re-exporting is easier (from an `index.js` for example):

With `export default`:

```js
import Foo from 'foo'
export Foo
```

Without `export default`:

```js
export * from 'foo'
```

5. Plays nice with commonjs users

With `export default`:

```js
import { default } = require('foo')
```

Without `export default`:

```js
import { Foo } = require('foo')
```

### And why do we break this rule for Components and Containers!?

According to the [directory structure guidelines](https://github.com/decentraland/standards/blob/master/standards/react-redux.md#components), for each `[Component].js` file we have an `index.js` file that exports the Component. This is intended for when a component needs to be connected to the redux store, we can just create a `[Component].container.js` file, connect the Component, and then just point the `index.js` file to the container.

We just want to get auto-imports/suggestions for the `index.js` file, and not for the other `[Component].js` and `[Component].container.js` files, that's why let those to `export default`, and then do a named export from the `index.js` file.
