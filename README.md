# Redux Tutorial

redux is very small lib

Tutorial base on commen way to use rudux in anyware
eg: react, angular, venila javascript

Course structure
1. Functional programming
2. Fundamentals of Redux.
3. Build Redux from scratch.
4. Debugging
5. Writing clean code.
6. Redux store
7. Middleware.
8. Calling APIs.
9. Testing Redux apps.
10. Integration with React.

## 1. Functional programming
Redux is build top of functional programming.

programming paradigms ()
* Functional(eg: Clojure,Haskell)
* Object-oriented
* Procedural
* Event-driven

Javascipt is multi paradigms programming language.

### Advantage of functional programming
1. More Concise.
2. Easier to Debug.(only input and make output)
3. Easier to Test.
4. More Scalable. (parallel take advantage of multiple core of CPU)


* Functional as First-class Citizen In javascipt.
  because,
  1. function can assign to a variable
  2. function can pass as an argument.
  3. function can return from other functions.


```javascipt
  function sayHello(){
    return "Hello World"
}

// ** function asing to variable
const fn = sayHello;
fn();
sayHello();

// ** pass the function as variable
function greet(fnMessage){
  console.log(fnMessage());
}
greet(sayHello);

// ** return function
function rtnFunction(){
    return function (){
        return "Hellow World";
    }
}
const message = rtnFunction();
```

when function input as argument or output as return thant function s call <mark>higher order</mark> function

```javascript
// eg : 1
const number = [1,2,3,4,5];
number.map(number=> number *2);

// eg : 2
setTimeout(()=>{
    console.log("Hello")
},1000);
```
## Function Composition

write small , reusable functions and compose them to build more complex functions.

```javascript
//  non functional composition
let input = "  JavaScript ";
let output = "<div> "+input.trim()+" </div>";

// functional composition
const trim = str => str.trim();
const wrapInDiv = str => "<div> "+str+" </div>";
const result  = wrapInDiv(trim(input));
```

## Composition and Piping.

in previous example we are getting many parentheses when composition programming. we can use lodash npmjs package for reslove this problem.



```javascript  
import { compose, pipe } from 'lodash/fp'; // fp= funtional progrmming


const trim = str => str.trim();
const wrapInDiv = str => "<div> "+str+" </div>";
const result  = wrapInDiv(trim(input));


const transformFromLeft  = compose(wrapInDiv, trim );
const transformFromRight = pipe(trim, wrapInDiv );
const lodashResult1 = transformFromLeft(input);
const lodashResult2 = transformFromRight(input);
```

above "compose, pipe" are only work for one argument function only. but we can pass
second argument as one argument function call "Currying"

## Currying

```javaScript
// normal function that have two arguments
function addNormal(a,b){
   return a+b;
}
// we are passing second argument as function
function addCurrying1(a){
    return function(b){
        return a+b;
    }
 }
// above function using arrow functions
const  addCurrying2 = a => b => a + b;

// const add = addCurrying(1);
// add(5); or following way
addCurrying1(1)(5);
```

## Pure Functions

* No random values.
* No current data/time.
* No global state (Dom, files, db, etc).
* no mutation of parameters.

```javaScript
// not a pure function. in future can be chage
function myFunction1 (number){
    return number * Math.random();
}

// pure function
function myFunction1 (number){
    return number * 2;
}

// not pure function with global variable(minAge) . in future can chage
function isEligible(age){
    return age> minAge;
}
```


### benefits
* self-documenting
* Easily testable.
* Concurrency
* Cacheable.

## Immutability

pure functions cannot change or mutate their arguments.
String are immutable, object and arrays are not immutable.

WHY IMMUTABILITY
* Predictability(no change of global variable)
* Faster Change Detection.
* Concurrency.

## Updating Object

```javascript
let name = "lanka";
// we are getting new string and current string not effected.
let newName = name.toUpperCase();

let book = {}; // note :   const keyword make prevents reassignment
book.title = " ";

// make object immutable
const person = {name:"Jhone"};
// preson object copy to {} and add new values to it.
const udpate1 = Object.assign({},preson,{name:"Elena"});
// create new persion object and add new values
const update2 = {...preson, name:"Bob"}

// ... oprater only create shadow of the object and it will effect nested objects

const preson ={
    name : "Jhone",
    address:{
        constry: "USA",
        city: "San Francisco"
    }
};

const updatedShadow = {...preson, name:"Irina"}

// following change effect to person object too.
updatedShadow.address.city = "Colombo";

// for prevnet it we have to make deep copy of preson object
const updated = {
    ...preson,
    address:{
        ...person.address,
        city:"New York" // new name of city
    },
    name: "Bob" // new name of preson
}

/** result
 const updated = {
     name: "Bob",
    address:{
        constry: "USA",
        city:"New York"
    }
}
 */

```

## Updating Arrays

```javaScript
const numbers = [1,2,3,4,5];

// *** Adding

// Adding at the end
const add1 = [...numbers,2];

// Adding at the start
const add2 = [2,...numbers];

// Adding specific place
const index =  numbers.indexOf(2);
const add3 = [...numbers.slice(0,index), 4, ...numbers.slice(index) ];

// *** Removing
const removed = numbers.filter(n=> n!=2);
console.log(removed);

// *** Updation
const updated= numbers.map(n=>(n===2 ? 20 : n));
console.log(updated);
```

## Enforcing Immutability

LIBRARIES
* Immutable (developed by facebook)
* Immer
* Mori


Immutable lib Map create new javascipt obj that need to use getter and setters
```javaScript
import { Map } from 'immutable';

let book = Map({title:"Harry Potter"});

function publish(book){
    return book.set("isPublished", true); // we have to make set function here

}

book = publish(book);
console.log(book.toJS()); // to convert plane javascript object
```

Immer lib
```javascript
import { produce  } from 'immer';

let book = {title:"Harry Potter"};

function publish(book){
    return produce(book, draftBook=>{
             draftBook.isPublished = true ;
           });
}

let updated = publish(book);

console.log(book);
console.log(updated); // book object not modified

```

## Redux Architecture

we store our all states in one place call store.
```javascipt
{
  categories:[],
  produce:[],
  cart:{},
  user:{}
}
```
we are use reducer function to update store

```javascript
// store : store that should update
// action : function to how to update store
function reducer(store, action){
  const updated = { ...store};
}
```
reducer is a function that get current instance of the store and returns the
updated store.

ACTION(event)--dispatch--> STORE<----> REDUCER(event handler)

we are direcly dealing with "ACTION" and it dispatch to "STORE" and make action.
And that action will forword to the "REDUCER". And "REDUCER" complete the new state
and return it to "STORE".Next soter will set the state internally and then notified
UI components.

## you First Redux Application.

STEPS
* Design the store.
* Define the action.
* Create a reducer.
* Set up the store.

to install redux
```sell
$ npm i redux@4.0
```


> index.js

```javascript
import store from './store';

//console.log(store)// you can see subscribe listener.  ui element will listen it.

// suscribe state (in react re render ui ) and function unsubscribe method
const unsuscribe = store.subscribe(()=>{
   console.log("Store changed!", store.getState());
});


// makeing action throught dispatch
store.dispatch({
    type:"ADD_BUG",
    payload:{
        description:"Bug1"
    }
});

unsuscribe(); // call unsubscribe for not futher listen

store.dispatch({
   type:"REMOVE_BUG",
   payload:{
       id:1
   }

});
```

> reducer.js

```javascript
let lastId = 0 ; // as a counter

export default  function reducer(state=[], action){ // this is a pure function.
    if(action.type === "ADD_BUG"){ // you can use switch case here
        return [
            ...state,
            {
               id : ++lastId,
               description : action.payload.description,
               resolved: false
            }
        ];
    }else if(action.type === "REMOVE_BUG"){
        return state.filter(bug=> bug.id !== action.payload.id)
    }

    return state; // for safty reson if action not found

}
```

>store.js

```javascript
import { createStore } from 'redux';
import reducer from './reducer';

const store = createStore(reducer);

export default store;
```


for maintainable reason we can get action name to one file call actionTypes.js[own name]
for maintainable reason we can create action file call actions.js[own name]


> index.js

```javascript
import store from './store';
import { bugAdded, bugRemoved}  from './actions';

//console.log(store)// you can see subscribe listener.  ui element will listen it.

// suscribe state (in react re render ui ) and function unsubscribe method
const unsuscribe = store.subscribe(()=>{
   console.log("Store changed!", store.getState());
});


// makeing action throught dispatch
store.dispatch(bugAdded("Bug1"));

//unsuscribe(); // call unsubscribe for not futher listen

store.dispatch(bugRemoved(1));
```

> reducer.js

```javascript
let lastId = 0 ; // as a counter
import * as actions from './actionTypes';

export default  function reducer(state=[], action){ // this is a pure function.
    if( action.type === actions.BUG_ADD ){ // you can use switch case here
        return [
            ...state,
            {
               id : ++lastId,
               description : action.payload.description,
               resolved: false
            }
        ];
    }else if(action.type === "REMOVE_BUG"){
        return state.filter(bug=> bug.id !== action.payload.id)
    }

    return state; // for safty reson if action not found
}
```

> store.js

```javascript
import { createStore } from 'redux';
import reducer from './reducer';

const store = createStore(reducer);

export default store;
```

> action.js

```javascript
import * as actions from './actionTypes';

export function bugAdded(description){
    return {
        type: actions.BUG_ADD, // name can change here and on "actionTypes.js" file
        payload:{
            description:description
        }
    }
}

// export function bugRemoved(id){
//     return {
//         type: actions.BUG_REMOVE,
//         payload:{
//             id:id
//         }
//     }    
// }

export const bugRemoved=id =>({     // wrapping with () for return object
        type: actions.BUG_REMOVE,
        payload:{
            id
        }
})
```

> actionTypes.js

```javascript
export const BUG_ADD = "ADD_BUG";
export const BUG_REMOVE = "REMOVE_BUG";
```
