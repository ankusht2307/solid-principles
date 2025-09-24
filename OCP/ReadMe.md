## Open–Closed Principle (OCP)

👉 Software entities (classes, functions, modules) should be open for extension but closed for modification.

#### That means:

- You shouldn’t change existing code to add new behavior.
- Instead, you should extend the code (inheritance, composition, interfaces, strategies, etc.).

This helps avoid breaking existing features when new requirements come in.

#### 🔎 Why does it matter?

- Stability: Existing, tested code remains untouched.
- Flexibility: New functionality is added without rewriting the old.
- Maintainability: Reduces bugs caused by modifying working code.

#### 🚫 Example (Violation of OCP)

```javascript
class Invoice {
  constructor(amount) {
    this.amount = amount;
  }

  calculateDiscount(type) {
    if (type === "regular") {
      return this.amount * 0.1;
    } else if (type === "premium") {
      return this.amount * 0.2;
    } else if (type === "vip") {
      return this.amount * 0.3;
    }
    return 0;
  }
}

const invoice = new Invoice(1000);
console.log(invoice.calculateDiscount("vip"));
```

#### 🔴 Problem:

- Every time a new customer type is added (e.g., student, senior citizen), we must modify calculateDiscount.
- Violates OCP → class is not closed for modification.

#### ✅ Refactored with OCP

We separate discount strategies and make Invoice depend on them.

```javascript
class RegularDiscount {
  calculate(amount) {
    return amount * 0.1;
  }
}

class PremiumDiscount {
  calculate(amount) {
    return amount * 0.2;
  }
}

class VipDiscount {
  calculate(amount) {
    return amount * 0.3;
  }
}

class Invoice {
  constructor(amount, discountStrategy) {
    this.amount = amount;
    this.discountStrategy = discountStrategy;
  }

  getFinalAmount() {
    return this.amount - this.discountStrategy.calculate(this.amount);
  }
}

// Usage:
const invoice = new Invoice(1000, new VipDiscount());
console.log(invoice.getFinalAmount()); // 700
```

#### 🔵 Benefits:

- If tomorrow you want StudentDiscount, just create a new class.
- No need to modify Invoice.
- Existing code is closed for modification, but you can extend with new discount strategies.

#### 🏗 Rule of Thumb

- Avoid “if/else” or “switch” for multiple behaviors → prefer polymorphism.
- Encapsulate what changes into new classes or strategies.
- Think plugins → existing system stays stable, new behavior plugs in.

#### 📌 OCP for Functions

👉 A function should be open for extension (new behaviors added) but closed for modification (you don’t rewrite existing code every time requirements change).

In practice:

- Avoid stuffing functions with growing if/else or switch statements.
- Instead, let functions accept strategies, callbacks, or configuration to extend behavior.

#### 🚫 Example (Violation of OCP)

```javascript
function calculateDiscount(type, amount) {
  if (type === "regular") {
    return amount * 0.1;
  } else if (type === "premium") {
    return amount * 0.2;
  } else if (type === "vip") {
    return amount * 0.3;
  }
  return 0;
}

console.log(calculateDiscount("vip", 1000));
```

#### 🔴 Problem:

If tomorrow we need a "student" discount, we must modify the function, which risks breaking tested code.

#### ✅ Refactored with OCP

Use strategies (functions as parameters).

```javascript
function calculateDiscount(amount, strategy) {
  return strategy(amount);
}

// Different discount strategies
const regularDiscount = (amount) => amount * 0.1;
const premiumDiscount = (amount) => amount * 0.2;
const vipDiscount = (amount) => amount * 0.3;

// Usage
console.log(calculateDiscount(1000, vipDiscount)); // 300
```

#### 🔵 Benefits:

- The calculateDiscount function is closed for modification.
- Adding a new discount (e.g., studentDiscount) means just writing a new function, not touching the old one.

## Another Example: Sorting with a Custom Rule

#### 🚫 Without OCP:

```javascript
function sortNumbers(arr, type) {
  if (type === "asc") {
    return arr.sort((a, b) => a - b);
  } else if (type === "desc") {
    return arr.sort((a, b) => b - a);
  }
}
```

#### ✅ With OCP:

```javascript
function sortNumbers(arr, comparator) {
  return arr.sort(comparator);
}

// Extend with new comparators
const asc = (a, b) => a - b;
const desc = (a, b) => b - a;
const evenFirst = (a, b) => (a % 2) - (b % 2);

console.log(sortNumbers([5, 2, 7, 4], evenFirst)); // [2,4,5,7]
```

#### 🏗 Rule of Thumb for Functions

- Accept strategies (callbacks, higher-order functions, config) instead of hardcoding logic.
- If you keep adding ifs/switches to a function for new behavior → you’re violating OCP.
- Think of functions as frameworks: stable core, extensible via inputs.

## Real World Examples

1. Array Methods (map, filter, sort, etc.)

👉 These are perfect examples of OCP in action.

```javascript
const numbers = [1, 2, 3, 4, 5];

// Map: open for extension with custom logic
const squares = numbers.map((n) => n * n);

// Filter: extend behavior with any condition
const evens = numbers.filter((n) => n % 2 === 0);

// Sort: extend behavior with a comparator
const desc = numbers.sort((a, b) => b - a);
```

✅ The core methods (map, filter, sort) never change.
We extend them with new callbacks → new behavior without modifying original code.

2. Event Listeners

OCP in the browser world:

```javascript
document.getElementById("btn").addEventListener("click", () => {
  console.log("Clicked!");
});
```

- The event system (addEventListener) is closed for modification.
- You extend it by adding new callback functions.
- You don’t rewrite browser internals to handle new logic.

3. Middleware in Express.js

If you’ve used Express in Node.js:

```javascript
app.use((req, res, next) => {
  console.log("Logging request...");
  next();
});
```

- Express core is closed for modification.
- You extend it by adding middleware functions.
- Each new feature (auth, logging, validation) is just a new function plug-in.

4. React Components with Props

React embraces OCP heavily.

```javascript
function Button({ label, onClick }) {
  return <button onClick={onClick}>{label}</button>;
}
```

- Button component is stable (closed for modification).
- You extend behavior by passing different onClick handlers.

```javascript
<Button label="Save" onClick={() => console.log("Saving...")} />
<Button label="Delete" onClick={() => console.log("Deleting...")} />
```

5. Custom Sort Example (Extending Functionality)

```javascript
function sortNumbers(arr, comparator) {
  return arr.sort(comparator);
}

const asc = (a, b) => a - b;
const desc = (a, b) => b - a;
const evenFirst = (a, b) => (a % 2) - (b % 2);

console.log(sortNumbers([7, 2, 5, 4], evenFirst)); // [2,4,7,5]
```

Here:

- sortNumbers is closed (no changes).
- We extend behavior by writing new comparator functions.

#### ✅ So, in real-world JS:

- Array methods → callbacks extend behavior.
- Events → listeners extend behavior.
- Express middleware → extend request/response cycle.
- React props → extend component behavior.

👉 All of these follow OCP naturally: stable core + extensibility points.
