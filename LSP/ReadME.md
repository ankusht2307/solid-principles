## Liskov Substitution Principle (LSP)

👉 Objects of a superclass should be replaceable with objects of a subclass without breaking the application.

In simpler words:

- If B is a subclass of A, then anywhere you use A, you should be able to use B without unexpected behavior.
- A subclass should honor the contract of its parent class.

#### 🔎 Why does it matter?

- Prevents broken inheritance hierarchies.
- Ensures predictable behavior when working with subclasses.
- Makes polymorphism safe and reliable.

#### 🚫 Example (Violation of LSP)

```javascript
class Bird {
  fly() {
    console.log("Flying...");
  }
}

class Sparrow extends Bird {}

class Penguin extends Bird {
  fly() {
    throw new Error("Penguins can’t fly!");
  }
}

// Usage
function makeBirdFly(bird) {
  bird.fly();
}

makeBirdFly(new Sparrow()); // ✅ Works
makeBirdFly(new Penguin()); // ❌ Breaks expectation
```

#### 🔴 Problem:

- Penguin is a Bird, but it breaks the contract (Bird promises fly).
- Substituting Penguin for Bird violates LSP.

#### ✅ Refactored with LSP

We separate behaviors so subclasses don’t need to break contracts.

```javascript
class Bird {
  // general bird stuff
}

class FlyingBird extends Bird {
  fly() {
    console.log("Flying...");
  }
}

class Sparrow extends FlyingBird {}

class Penguin extends Bird {
  swim() {
    console.log("Swimming...");
  }
}

// Usage
function makeFlyingBirdFly(bird) {
  bird.fly();
}

makeFlyingBirdFly(new Sparrow()); // ✅ Works
// makeFlyingBirdFly(new Penguin()); ❌ Not allowed now
```

#### 🔵 Benefits:

- Sparrow and Penguin both follow LSP.
- Penguin no longer breaks expectations — it doesn’t pretend to be a flying bird.

#### 🏗 Rule of Thumb

- Subclasses should not weaken preconditions (don’t require more than parent).
- Subclasses should not strengthen postconditions (don’t return less useful results).
- Avoid throwing “NotImplemented” or breaking parent promises.

## 📌 LSP for Functions

👉 A function that accepts a parameter or returns a value should be able to work with substitutable types without breaking expectations.

That means:

- A function should not behave incorrectly if it’s given a subtype (or something that “acts like” the expected type).
- Subtypes should honor the contract implied by the function’s parameter/return type.

#### 🚫 Example (Violation of LSP)

```javascript
function printBirdInfo(bird) {
  console.log("This bird can fly: " + bird.fly());
}

const sparrow = { fly: () => true };
const penguin = {
  fly: () => {
    throw new Error("Penguins can’t fly");
  },
};

printBirdInfo(sparrow); // ✅ Works
printBirdInfo(penguin); // ❌ Breaks expectation
```

#### 🔴 Problem:

- printBirdInfo assumes all birds can fly.
- Passing penguin violates LSP because it breaks the contract.

#### ✅ Refactored with LSP

```javascript
function printBirdInfo(bird) {
  if (bird.fly) {
    console.log("This bird can fly: " + bird.fly());
  } else if (bird.swim) {
    console.log("This bird can swim: " + bird.swim());
  }
}

const sparrow = { fly: () => true };
const penguin = { swim: () => true };

printBirdInfo(sparrow); // ✅ "This bird can fly: true"
printBirdInfo(penguin); // ✅ "This bird can swim: true"
```

🔵 Now the function respects different contracts: flying birds and swimming birds. No surprises.

#### Another Example: Math Functions

🚫 Violation:

```javascript
function squareRoot(x) {
  if (x < 0) throw new Error("No negatives allowed!");
  return Math.sqrt(x);
}

console.log(squareRoot(9)); // ✅ 3
console.log(squareRoot(-9)); // ❌ Violates expectation if user thought negatives are ok
```

#### ✅ With clear contracts:

```javascript
function squareRoot(x) {
  return Math.sqrt(Math.abs(x)); // ensures contract "always returns a real number"
}

console.log(squareRoot(-9)); // ✅ 3
```

Now the function works with all numbers (subtypes of Number) consistently.

#### 🏗 Rule of Thumb for Functions

1. Input contract: Don’t require more than promised.
   - If a function says it accepts an Array, don’t break if someone passes a TypedArray or subclass.
2. Output contract: Return values should be substitutable.
   - If you say a function returns a Number, don’t sometimes return a boolean or string.
3. Avoid “surprise” behavior.
   - Don’t throw unexpected errors just because the input was a valid subtype.

#### ✅ Real-World JavaScript Example

Example: Shapes

🚫 Violation:

```javascript
class Rectangle {
  constructor(width, height) {
    this.width = width;
    this.height = height;
  }

  area() {
    return this.width * this.height;
  }
}

class Square extends Rectangle {
  constructor(size) {
    super(size, size);
  }
}

// Usage
const rect = new Rectangle(4, 5);
rect.width = 10; // fine

const square = new Square(5);
square.width = 10; // ❌ Now height should also change, breaks logic
```

#### ✅ Fix:

```javascript
class Shape {
  area() {
    throw new Error("Not implemented");
  }
}

class Rectangle extends Shape {
  constructor(width, height) {
    super();
    this.width = width;
    this.height = height;
  }
  area() {
    return this.width * this.height;
  }
}

class Square extends Shape {
  constructor(size) {
    super();
    this.size = size;
  }
  area() {
    return this.size * this.size;
  }
}
```

Now both Rectangle and Square can substitute Shape without surprises.

## More Examples

#### 1. DOM Events (Good Example — LSP honored)

The DOM event system is designed with LSP in mind:

```javascript
function handleEvent(event) {
  console.log("Type:", event.type);
}

document.addEventListener("click", handleEvent);
document.addEventListener("keydown", handleEvent);
```

- handleEvent expects an Event.
- Both MouseEvent (click) and KeyboardEvent (keydown) are subtypes of Event.
- ✅ LSP holds → any event can substitute Event without breaking.

#### 2. Promises (Good Example — LSP honored)

Promise works consistently no matter what you return inside .then():

```javascript
Promise.resolve(5)
  .then((num) => num * 2) // returns number
  .then((result) => Promise.resolve(result + 1)) // returns Promise
  .then((final) => console.log(final)); // ✅ 11
```

- .then() accepts both raw values and promises.
- ✅ Substitution works (any subtype of return is fine).

#### 3. Array-Like Objects (Violation Example)

Many DOM APIs return “array-like” objects, but they don’t behave like true arrays.

```javascript
const nodeList = document.querySelectorAll("div");

console.log(nodeList.map); // ❌ undefined
```

- NodeList looks like an Array (has .length, index access), but doesn’t implement .map, .filter, etc.
- ❌ Violates LSP: You can’t substitute a NodeList where an Array is expected.
- Fix:

```javascript
Array.from(nodeList).map((div) => div.textContent);
```

#### 4. Date in JavaScript (Violation Example)

Date is technically a subclass of Object, but it behaves strangely:

```javascript
console.log({} + 1); // "[object Object]1"
console.log(new Date() + 1); // "Mon Sep ... 1"
```

- Date substitution doesn’t behave like a “normal object”.
- The contract of Object (toString) is violated → ❌ LSP break.

#### 5. Subclassing Built-ins (Common Pitfall)

JavaScript lets you subclass built-ins (like Array), but behavior sometimes breaks expectations.

```javascript
class MyArray extends Array {}
const arr = new MyArray(1, 2, 3);

console.log(arr instanceof Array); // ✅ true
console.log(arr.map((x) => x * 2) instanceof MyArray); // ✅ returns MyArray
```

Looks fine, but:

```javascript
console.log([].concat(arr) instanceof MyArray); // ❌ false
```

- concat drops the subclass → violates substitutability.

#### ✅ Key Takeaway

LSP violations often appear in JavaScript when:

- APIs return “array-like” but not true arrays (NodeList).
- Built-ins behave inconsistently (Date).
- Subclassing built-ins leads to broken expectations (Array subclasses).

👉 Always design functions/classes so that subtypes can stand in for supertypes without breaking client code.
