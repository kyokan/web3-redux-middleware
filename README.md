# Web3 Redux Middleware

[![npm version]()]() [![Build Status]()]() [![npm downloads]()]()

PromiEvent-redux Middleware enables robust handling of async action creators in [Redux](http://redux.js.org): it accepts a promiEvent and dispatches pending, fulfilled and rejected actions.
Using Web3 Redux Middleware allows for declarative Reducers to handle Web3.js method sends.

TODO add confirmation number property to _CONFIRMATION actions
TODO support .call() as well as .send()
TODO more unit tests

## Example
```js
import { createStore, applyMiddleware } from 'redux'
import web3Redux from 'web3-redux-middleware'

const store = createStore(
  promiEventReducer,
  {},
  applyMiddleware(web3Middleware())
);

const PROMI_EVENT = 'PROMI_EVENT';

const contract = new web3.eth.Contract(abi);

// To Use just attatch a PromiEvent as a payload

const promiEventAction = () => ({
  type: PROMI_EVENT,
  payload: contract
    .methods
    .contractMethod()
    .send({
      from: `0xE71b3853e8Dbc57Bc91E7504726e8F051c977e69`,
      value: 1000000
    })
});

// The middleware will intercept the action and dispatch actions as the network events occur

function promiEventReducer(state, action) {
  // A _PENDING event will be sent right away
  switch(action.type) {
    case `${PROMI_EVENT}_PENDING`:
      return state;

    // Other events will be released as the network events occur
    case `${PROMI_EVENT}_FULFILLED`:
      return {
        ...state,
        result: action.payload
      };

    case `${PROMI_EVENT}_HASHED`:
      return {
        ...state,
        hash: action.payload
      };

    case `${PROMI_EVENT}_CONFIRMED`:
      return {
        ...state,
        confirmationNumber: action.payload
      };

    case `${PROMI_EVENT}_RECEIPT`:
      return {
        ...state,
        receipt: action.payload
      };

    // Dispatched on .catch
    case `${PROMI_EVENT}_REJECTED`
      return {
        ...state,
        value: action.payload,
        isError: action.error,
      };

    // Dispatched on 'error' event
    case `${PROMI_EVENT}_ERROR`:
      return {
        ..state,
        value: action.payload,
        isError: action.error
      }
  }
};

// Alternatively you can attatch the promise to a promiEvent to the promiEvent property
// Common use case is when you want to send some data for optimistic loading
// The data will be dispatched on the _PENDING action
const promiEventAction2 = () => ({
  type: PROMI_EVENT,
  payload: {
    data: 'Some Data',
    promiEvent: contract
      .methods
      .contractMethod()
      .send({
        from: `0xE71b3853e8Dbc57Bc91E7504726e8F051c977e69`,
        value: 1000000
      })
});

// Any meta data can be attatched the the meta property.  It will be dispatched on every event
const promiEventAction3 = () => ({
  type: PROMI_EVENT,
  meta: 'some meta data'
  payload: contract
    .methods
    .contractMethod()
    .send({
      from: `0xE71b3853e8Dbc57Bc91E7504726e8F051c977e69`,
      value: 1000000
    })
})

// to change the promise type suffixes or the delimiter just pass in a config object
const configedWeb3Middleware = web3Redux({
  promiseTypeDelimiter: ' ',
  promiseTypeSuffixes: {
    pending: 'WAITING',
    fulfilled: 'SUCCESS',
    rejected: 'FAIL',
    transactionHash: 'TRANSACTION_HASHED',
    confirmation: 'CONFIRMATION',
    reciept: 'RECEIPT_RECIEVED',
    error: 'FAILURE'
  }
})

```

## Releases

- [Releases](TODO)

## Maintainers

- William Cory (roninjin10):
  - [Twitter](https://twitter.com/roninjin10)
  - [GitHub](https://github.com/roninjin10)

Please reach out if you have any questions or comments!

## License

TODO