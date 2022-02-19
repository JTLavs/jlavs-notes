---
title: Closures
date: "2022-02-15"
tags: ["javascript"]
---

- A closure is a function which contains its own lexical environment.
- It gives you access to the outer functions scope from an inner function.
- The term lexical refers to the fact that lexical scoping uses the location where a variable is declared within the source code to determine where that variable is available. 

```javascript 
function adder(x) {
  return function(y) {
    // the inner function can access the outer functions scope.
    return x+y; 
  }
}

var add5 = adder(5);
add5(5); // returns 10

var add10 = adder(10);
add10(5); // returns 15
```

- `add(5)` is a closure function and within its lexical environment `x` is 5.
- `add(10)` is another closure function and operates in the same manner as `add(5)` but within its lexical environment `x` is 10.