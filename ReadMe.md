# SOLID Principles Cheat Sheet (JavaScript)

![SOLID](/solid.png)

## Principle

#### S – Single Responsibility Principle (SRP)

## Meaning

A class/function should have **one reason to change.**

## Class Example

```javascript
class User {
  constructor(name) {
    this.name = name;
  }
  getInfo() {
    return this.name;
  }
}
class UserRepo {
  save(user) {
    console.log("Saving");
  }
}
```

## Functional Example

```javascript
function createUser(name) {
  return { name };
}
function saveUser(user) {
  console.log("Saving");
}
```

## Principle

#### O – Open/Closed Principle (OCP)

## Meaning

Code should be **open for extension, closed for modification.**

## Class Example

```javascript
class Invoice {
  constructor(amount, discount) {
    this.amount = amount;
    this.discount = discount;
  }
  final() {
    return this.amount - this.discount.calculate(this.amount);
  }
}
```

## Functional Example

```javascript
function calculateDiscount(amount, strategy) {
  return strategy(amount);
}
const vip = (a) => a * 0.3;
calculateDiscount(1000, vip);
```

## Principle

#### L – Liskov Substitution Principle (LSP)

## Meaning

Subtypes should be **replaceable** without breaking the program.

## Class Example

```javascript
class Bird {}
class FlyingBird extends Bird {
  fly() {
    console.log("Fly");
  }
}
class Sparrow extends FlyingBird {}
```

## Functional Example

```javascript
function handleBird(bird) {
  if (bird.fly) bird.fly();
}
handleBird({ fly: () => console.log("Fly") });
```

## Principle

#### I – Interface Segregation Principle (ISP)

## Meaning

Clients should **not depend on methods they don’t use.**

## Class Example

```javascript
class Printer {
  print() {}
}
class Scanner {
  scan() {}
}
class MultiFunctionPrinter extends Printer {
  scan() {}
}
```

## Functional Example

```javascript
const Printer = (obj) => ({ print: obj.print });
const Scanner = (obj) => ({ scan: obj.scan });
```

## Principle

#### D – Dependency Inversion Principle (DIP)

## Meaning

**High-level modules** should not depend on **low-level modules**; both depend on **abstractions**.

## Class Example

```javascript
class UserService {
  constructor(db) {
    this.db = db;
  }
  createUser(u) {
    this.db.save(u);
  }
}
class MySQL {
  save(u) {
    console.log("MySQL");
  }
}
```

## Functional Example

```javascript
function createUserService(saveFn) {
  return { createUser: (u) => saveFn(u) };
}
createUserService((u) => console.log("Save"));
```
