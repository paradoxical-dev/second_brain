---
id: Set_js
aliases: []
tags: []
---

#Summary
    `Set` is a builtin collection type that makes it easy to store unique values
    of any type, both primitive values or object references. It automatically
    removes duplicate values ensuring it is unique.

#Set Creation
Creating a set is done with the `Set` constructor
```javascript
const mySet = new Set();
```
You can initialize a `Set` with an iterable to sutomatically remove duplicates
```javascript
const mySet = new Set([1,2,3,4,4,5]);
console.log(mySet); //output: Set(5) {1,2,3,4,5}
```

#Methods and Properties of Set
`add(value)`
    - adds a new element with the given value to the `Set`.
```javascript
    const mySet = new Set();
    mySet.add(1);
    mySet.add(2);
    mySet.add(2);
    // duplicate will be ignored
    console.log(mySet) // output: Set(2) {1, 2}
```
`delete(value)`
    - removes the specific element from `Set`
```javascript
    const mySet = new Set([1,2,3]);
    mySet.delete(2);
    console.log(mySet); //output: Set(2) {1, 3}
```
`has(value)`
    - returns boolean for if `Set` contains value
```javascript
    const mySet = new Set([1,2,3]);
    console.log(mySet.has(4)); // output: false
```
`clear()`
    - removes all elements from `Set`
```javascript
    const mySet = new Set([1,2,3]);
    mySet.clear();
    console.log(mySet); // output: Set(0) {}
```
`size`
    - returns number of elements in `Set`
```javascript
    console mySet = new Set([1,2,3])
    console.log(mySet.size); // output: 3
```
You can also iterate over elements in a set using different methods (forEach, for ... of, etc.)
or, transfer the Set to a different storage type such as an array:
```javascript
const mySet = new Set([1,2,3]);
const myArray = [...mySet];
console.log(myArray); // output: [1.2.3]
```
* `...` represents the [[spread_operator_js]]

#Practical Examples
- Remove Duplicates From Array
```javascript
    const numbers = [1,2,2,3,4,4,5];
    const uniqueNumbers = [...new Set(numbers)];
    console.log(uniqueNumbers) // output: [1,2,3,4,5]
```
- Union of Two Sets
```javascript
    const setA = new Set([1,2,3]);
    const setB = new Set([3,4,5]);
    const union = new Set([...setA, ...setB]);
    console.log(union) // output: Set(5) {1,2,3,4,5}
```
- Intersection of Two Sets
```javascript
    const setA = new Set([1,2,3]);
    const setB = new Set([2,3,4]);
    const intersection = new Set([...setA].filter(x => setB.has(x)));
    console.log(intersection); // output: Set(2) {2, 3}
```
    * this uses the [[filter_js]] method to only return values where the sets interact

- Difference of Two Sets
```javascript
    const setA = new Set([1,2,3]);
    const setB = new Set([3,4,5]);
    const diff = new Set([...setA].filter(x => !setB.has(x)));
    console.log(diff); // output: Set(2) {1, 2}
```
