---
id: spread_operator_js
aliases: []
tags: []
---

#Summary
    the spread operator `...` allows an iterable to be expanded in places where
    zero or more arguments or elements are expected.

#In Arrays
- Converting to array
```javascript
const mySet = new Set([1,2,3]);
const myArray = [...mySet];
console.log(myArray); // output: [1,2,3]
```
    - here the spread operator is used to spread the elements of the `Set` to an array

- Concattenating Arrays
```javascript
const array1 = [1,2,3];
const array2 = [4,5,6];
const combined = [...array1, ...array2];
console.log(combined) // output: [1,2,3,4,5,6]
```
- Copying Arrays
```javascript
const og = [1,2,3];
const copy = [...og0;
console.log(copy) // output: [1,2,3]
```
