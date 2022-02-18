---
title: Typescript notes
date: "2022-02-15"
---
## Types
#### Tuple
```
let a = [x: number, y: string]
a = [1, 'hello world']
a = ['hello', 'world'] //error
```

#### Null and undefined types
`null` and `undefined` are subtypes of all other types, so can be assigned to `number` or `string` only when the `--strictNullChecks` flag is not used if this flag is used then only assignable to `unknown` and `any`.

#### Never type
`never` type are for values that never occur. eg. a function that throws an error.
`never` is a subtype of all other types but no type is a subtype of `never`.

## Interfaces
Type checking in Typescript focuses on the shape of the objects. 
Sometimes called duck typing or structutral typing.
This still works as the object passed in has the required property for the function, even though the object has additional fields:

```interface LabeledValue {
 label: string;
 size: number;
}

function printLabel(labeledObj: LabeledValue) {
  console.log(labeledObj.label);
}

let myObj = { size: 10, label: "Size 10 Object" };
printLabel(myObj);
```

Optional properties are denoted with a `?` after their name.
The advantage of optional properties is that you can describe these possibly available properties while still also preventing use of properties that are not part of the interface. 

```
interface SquareConfig {
 color? : string;
 width?: number;
}
```

### Readonly properties
Readonly properties can only be changed when ano bject is first created. Specified by putting readonly before the name:
```
interface Point {
 readonly x: number;
}
```

You can then no longer change the variable after it has been first assigned:
```
let p: Point = { x:2 }
p.x = 3 //error
```

### Readonly arrays
There is also a `ReadonlyArray<T>` type which is just an `Array` type with all mutating methods removed.

```
let a: ReadOnlyArray<number> = [1,2,3,4];
let b: number[] = [1,2,3,4];

a.push(5); // error
a[0] = 2; // error
a.length; //error
b = a; //error

// Using a type assertion to assign a readonly array to another array works
b = a as number[];
```
`readonly` should be used on properties as opposed to `const` which should be used for variables.

## Classes
### Protected modifier

The protected modifier is similar to the provate modifier except properties declared `protected` can also be accessed in a deriving class.

```
class Person {
  protected name: string;
  constructor(name: string) {
    this.name = name;
  }
}

class Employee extends Person {
  private department: string;
  constructor(name: string, department: string) {
    super(name);
    this.department = department;
  }

  public getInformation() : string {
    return `Name: ${this.name} - Department: ${this.department}`;
  }
}
```
We can stil use the name property from within derived class `Employee` as it is protected. But doing `employee.name` would result in an error.

### Constructor functions
When you declare a class in TypeScript, you are creating multiple declarations simutanously. The first is the type of the instance of the class. 

```
class Dog {
  name: string;
  constructor(name: string) {
    this.name = name
  }

  info() {
    return "The dogs name is" + this.name;
  }
}

let dog: Dog;
dog = new Dog('Max');
console.log(dog.info());
```

Here when `dog` is declared we are using `Dog` as the type of instances of the class `Dog`.
We are also creating another value called the constructor function. This is called when instantiate using `new`.

```
// The constructor function in Javascript
// created by the above example:
 
let Dog = (function () {
  function Dog(name) {
    this.name = name;
  }

  Dog.prototype.info = function() {
    return "The dogs name is" + this.name;
  }

  return Dog;
})();

let dog: Dog;
dog = new Dog('Max');
console.log(dog.info())
```