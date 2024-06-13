---
id: map_js
aliases: []
tags: []
---

#Summary
    `map` is a builtin method belonging to the `array` prototype. It creates
    a new array from performing a function on each item in the given array making
    it useful for transforming or computing data for elements in an array,
    without modifying the original array.

#Syntax
```javascript
array.map(callback(currentValue, index, array), thisArg);
```
    - `array` is the array you're using map wwih
    - `callback` is the function called on each element in the array; it takes 3 arguments
- `currentValue` the current item in the array
        - `index` (optional) index of the current item in the array
        - `array` (optional) the array `map` was called upon
    - `thisArg` (optional) a value to use as `this` in the `callback` function

#How Does It Work
    1. no changes are made to original array, a new array is created
    2. execute callback once for each iten in array
    3. push each of resulting items to new array

#Ex1
```javascript
const numbers = [1,2,3,4,5];

const square = (num) => num * num;

const squaredNumbers = numbers.map(square);

console.log(squaredNumbers); // output: [1.4.9.16,25]
```

- `numbers` represents the original array and `square` represents the callback
- `squaredNumbers` calls the map method which calls 'square' on each of the items in the array and pushes them to a new array

#Ex2
```javascript
const letters = ['a','b','c','d'];

const indexedLetters = letters.map((letter, index) => {
    return `${index}: ${letter}`;
});

console.log(indexedLetters); //output: ['0:a', '1:b', '2:c', '3:d']
```

- in this example the callback uses `letter` to represent the current item and `index` to represent the index of this item
- the callback uses an anonymous function which prints both of these vales and pushes them as strings to the new array
