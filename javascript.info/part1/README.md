# JavaScript

### what is transpiler
 A transpiler is a special piece of software that translates source code to another source code. It can parse (“read and understand”) modern code and rewrite it using older syntax constructs, so that it’ll also work in outdated engines.

### what is polyfills
New language features may include not only syntax constructs and operators, but also built-in functions.
In some (very outdated) JavaScript engines, there’s no Math.trunc, so such code will fail.

As we’re talking about new functions, not syntax changes, there’s no need to transpile anything here. We just need to declare the missing function.

`A script that updates/adds new functions is called “polyfill”. It “fills in” the gap and adds missing implementations.`

```javascript
if (!Math.trunc) { // if no such function
  // implement it
  Math.trunc = function(number) {
    // Math.ceil and Math.floor exist even in ancient JavaScript engines
    // they are covered later in the tutorial
    return number < 0 ? Math.ceil(number) : Math.floor(number);
  };
}
```

## Object Cloning

There are multiple ways to clone an object in JavaScript. Below are some common approaches:

### Using a `for...in` loop
```javascript
let user = {
  name: "John",
  age: 30
};

let clone = {}; // the new empty object

// Copy all properties from 'user' to 'clone'
for (let key in user) {
  clone[key] = user[key];
}
```
### Using Object.assign()

```javascript
let user = { name: "John" };

let permissions1 = { canView: true };
let permissions2 = { canEdit: true };

// Copies all properties from permissions1 and permissions2 into 'user'
Object.assign(user, permissions1, permissions2);

// Cloning using Object.assign()
let user = {
  name: "John",
  age: 30
};

let clone = Object.assign({}, user);
```

### Using the Spread Operator (...)

```javascript
let user = {
  name: "John",
  age: 30
};

let clone = { ...user };
```

## Nested Object Cloning
When dealing with nested objects, a simple shallow clone (as shown above) won’t be enough, since nested objects remain references. To perform a deep clone, use `structuredClone()`:

```javascript
let user = {
  name: "John",
  sizes: {
    height: 182,
    width: 50
  }
};

// Deep cloning the object
let clone = structuredClone(user);

// Checking if the nested objects are independent
console.log(user.sizes === clone.sizes);  // false
```
`Note:` Cloning will fail if the object contains function properties, as functions are not serializable with structuredClone().

### 4 what is unreachable island?
It is possible that the whole island of interlinked objects becomes unreachable and is removed from the memory.

### this keyword `this is not bound`

In JavaScript, keyword this behaves unlike most other programming languages. It can be used in any function, even if it’s not a method of an object.

The value of this is evaluated during the run-time, depending on the context.

`In JavaScript this is “free”, its value is evaluated at call-time and does not depend on where the method was declared, but rather on what object is “before the dot”.
`

### Arrow function has no this
```javascript
let user = {
    firstName: "Ilya",
    
  };
  
  function sayHi() {
    console.log(this.firstName); // Ilya
    let arrow = () => {
        console.log(this.firstName); //Ilya
    }
    function notArrow() {
        console.log(this.firstName);  // undefined
    }
    arrow();
    notArrow();
  }

  user.f = sayHi;
  user.f();
```

## Constructor, operator "new" - for creating object
### what is constructor function?
Constructor functions technically are regular functions. There are two conventions though:

1. They are named with capital letter first.
2. They should be executed only with "new" operator.

```javascript
function User(name) {
  // this = {};  (implicitly)

  // add properties to this
  this.name = name;
  this.isAdmin = false;

  // return this;  (implicitly)
}
let user = new User("Jack");

console.log(user.name); // Jack
console.log(user.isAdmin); // false
```
`Inside a function, we can check whether it was called with new or without it, using a special new.target property.`
```javascript
function User() {
  console.log(new.target);
}

// without "new":
User(); // undefined

// with "new":
new User(); // function User { ... }
```

```javascript
function User(name) {
  if (!new.target) { // if you run me without new
    return new User(name); // ...I will add new for you
  }

  this.name = name;
}

let john = User("John"); // redirects call to new User
console.log(john.name); 
```

### what is short-circuiting
Optional chaining(?.) immediately stops (“short-circuits”) the evaluation if the left part doesn’t exist. So, if there are any further function calls or operations to the right of ?., they won’t be made.

### Object to primitive
https://javascript.info/object-toprimitive

```javascript
let obj = {
  // toString handles all conversions in the absence of other methods
  toString() {
    return "2";
  }
};

console.log(obj * 2); // 4, object converted to primitive "2", then multiplication made it a number
console.log(obj + 2); // "22" ("2" + 2), conversion to primitive returned a string => concatenation
```

The object-to-primitive conversion is called automatically by many built-in functions and operators that expect a primitive as a value.

There are 3 types (hints) of it:

"string" (for console.log and other operations that need a string)
"number" (for maths)
"default" (few operators, usually objects implement it the same way as "number")
The specification describes explicitly which operator uses which hint.

The conversion algorithm is:

Call obj[Symbol.toPrimitive](hint) if the method exists,
Otherwise if hint is "string"
try calling obj.toString() or obj.valueOf(), whatever exists.
Otherwise if hint is "number" or "default"
try calling obj.valueOf() or obj.toString(), whatever exists.
All these methods must return a primitive to work (if defined).

In practice, it’s often enough to implement only obj.toString() as a “catch-all” method for string conversions that should return a “human-readable” representation of an object, for logging or debugging purposes.

### what is symbol type?
By specification, only two primitive types may serve as object property keys:
- string type
- symbol type

A “symbol” represents a unique identifier.
A value of this type can be created using Symbol():
```javascript
let id = Symbol();
```

### what is global symbol?
As we’ve seen, usually all symbols are different, even if they have the same name. But sometimes we want same-named symbols to be same entities. For instance, different parts of our application want to access symbol "id" meaning exactly the same property.

To achieve that, there exists a global symbol registry. We can create symbols in it and access them later, and it guarantees that repeated accesses by the same name return exactly the same symbol.

In order to read (create if absent) a symbol from the registry, use Symbol.for(key).

```javascript
// read from the global registry
let id = Symbol.for("id"); // if the symbol did not exist, it is created

// read it again (maybe from another part of the code)
let idAgain = Symbol.for("id");

// the same symbol
console.log( id === idAgain ); // true
```

```javascript
// get symbol by name
let sym = Symbol.for("name");
let sym2 = Symbol.for("id");

// get name by symbol
console.log( Symbol.keyFor(sym) ); // name
console.log( Symbol.keyFor(sym2) ); // id
```

```javascript
let globalSymbol = Symbol.for("name");
let localSymbol = Symbol("name");

console.log( Symbol.keyFor(globalSymbol) ); // name, global symbol
console.log( Symbol.keyFor(localSymbol) ); // undefined, not global

console.log( localSymbol.description );
```

`Symbol is a primitive type for unique identifiers.
Symbols are created with Symbol() call with an optional description (name).
Symbols are always different values, even if they have the same name. If we want same-named symbols to be equal, then we should use the global registry: Symbol.for(key) returns (creates if needed) a global symbol with key as the name. Multiple calls of Symbol.for with the same key return exactly the same symbol.`

### primitives in javascript.
There are 7 primitive types: string, number, bigint, boolean, symbol, null and undefined.

## Numbers

In modern JavaScript, there are two types of numbers:
- Regular numbers in JavaScript are stored in 64-bit format, also known as “double precision floating point numbers”. 
- BigInt numbers represent integers of arbitrary length. They are sometimes needed because a regular integer number can’t safely exceed (253-1) or be less than -(253-1), as we mentioned earlier in the chapter Data types. 

**Hex, binary and octal numbers**
```javascript
console.log( 0xFF ); //  255


let a = 0b11111111; // binary form of 255
let b = 0o377; // octal form of 255

console.log( a == b ); // true, the same number 255 at both sides
```
`Hexa, Binary and octal numeral systems are supported using the Ox, 0b and 0o prefixes:`

**toString(base)**
```javascript
let num = 255;

console.log( num.toString(16) );  // ff
console.log( num.toString(2) );   // 11111111
```
`The base can vary from 2 to 36. By default, it’s 10.`

**parseInt and parseFloat**
They “read” a number from a string until they can’t. In case of an error, the gathered number is returned.

## Strings

- str.indexOf(substr, pos).
- includes, startsWith, endsWith
- str.slice(start [, end])
- str.substring(start [, end])
- str.substr(start [, length])
- str.codePointAt(pos)
- str.localeCompare(str2) 

## Arrays
- arr.push(...items) – adds items to the end,
- arr.pop() – extracts an item from the end,
- arr.shift() – extracts an item from the beginning,
- arr.unshift(...items) – adds items to the beginning.
- arr.splice(start[, deleteCount, elem1, ..., elemN])
- arr.slice([start], [end])
- arr.concat(arg1, arg2...)
- arr.forEach
```javascript
arr.forEach(function(item, index, array) {
  // ... do something with an item
});
```
- indexOf/lastIndexOf and includes
- find and findIndex/findLastIndex
- arr.filter
```javascript
arr.filter(function(item, index, array) {
  // if true item is pushed to results and the iteration continues
  // returns empty array if nothing found
});
```
- arr.map
```javascript
arr.map(function(item, index, array) {
  // returns the new value instead of item
});
```
- arr.sort(copmarableFunc) 
- arr.reverse();
- arr.split(delim)
- arr.join(glue) 
- arr.reduce/ arr.reduceRight
```javascript
arr.reduce(function(accumulator, item, index, array) {
  // ...
}, [initial]);
```
- Array.isArray

### iterables
https://javascript.info/iterable

## Map and Set

- new Map() – creates the map.
- map.set(key, value) – stores the value by the key.
- map.get(key) – returns the value by the key, undefined if key doesn’t exist in map.
- map.has(key) – returns true if the key exists, false otherwise.
- map.delete(key) – removes the element (the key/value pair) by the key.
- map.clear() – removes everything from the map.
- map.size – returns the current element count.

### Iteration over Map
For looping over a map, there are 3 methods:

- map.keys() – returns an iterable for keys,
- map.values() – returns an iterable for values,
- map.entries() – returns an iterable for entries [key, value], it’s used by default in for..of.

```javascript
recipeMap.forEach( (value, key, map) => {
  alert(`${key}: ${value}`); // cucumber: 500 etc
});

let map = new Map([
  ['1',  'str1'],
  [1,    'num1'],
  [true, 'bool1']
]);

let obj = {
  name: "John",
  age: 30
};

let map = new Map(Object.entries(obj));
```

A Set is a special type collection – “set of values” (without keys), where each value may occur only once.

Its main methods are:

- new Set([iterable]) – creates the set, and if an iterable object is provided (usually an array), copies values from it into the set.
- set.add(value) – adds a value, returns the set itself.
- set.delete(value) – removes the value, returns true if value existed at the moment of the call, otherwise false.
- set.has(value) – returns true if the value exists in the set, otherwise false.
- set.clear() – removes everything from the set.
- set.size – is the elements count.

## WeakMap and WeakSet

WeakMap is Map-like collection that allows only objects as keys and removes them together with associated value once they become inaccessible by other means.

WeakSet is Set-like collection that stores only objects and removes them once they become inaccessible by other means.

### Object.keys, values, entries
For plain objects, the following methods are available:

- Object.keys(obj) – returns an array of keys.
- Object.values(obj) – returns an array of values.
- Object.entries(obj) – returns an array of [key, value] pairs.

Transforming objects
Objects lack many methods that exist for arrays, e.g. map, filter and others.

If we’d like to apply them, then we can use Object.entries followed by Object.fromEntries:

Use Object.entries(obj) to get an array of key/value pairs from obj.
Use array methods on that array, e.g. map, to transform these key/value pairs.
Use Object.fromEntries(array) on the resulting array to turn it back into an object.
```javascript
let prices = {
  banana: 1,
  orange: 2,
  meat: 4,
};

let doublePrices = Object.fromEntries(
  // convert prices to array, map each key/value pair into another pair
  // and then fromEntries gives back the object
  Object.entries(prices).map(entry => [entry[0], entry[1] * 2])
);

alert(doublePrices.meat); 
```

### Date and time
https://javascript.info/date

### JSON methods, toJSON
https://javascript.info/json