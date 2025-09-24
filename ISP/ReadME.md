## Interface Segregation Principle (ISP)

#### 📌 What is ISP?

👉 Clients should not be forced to depend on methods they do not use.

In plain words:

- Don’t create big, fat interfaces that make users implement irrelevant stuff.
- Instead, split them into smaller, more specific contracts.
- Works with both OOP interfaces and functional composition in JavaScript.

#### 🚫 Example (Violation of ISP)

Imagine you define one big interface (or contract) for different types of machines:

```javascript
// Suppose we expect all machines to implement this
function MachineInterface(machine) {
  return {
    print: machine.print,
    scan: machine.scan,
    fax: machine.fax,
  };
}

// Simple printer
const basicPrinter = {
  print: () => console.log("Printing..."),
  // Forced to include unused methods
  scan: () => {
    throw new Error("Not supported");
  },
  fax: () => {
    throw new Error("Not supported");
  },
};

basicPrinter.print(); // ✅ works
basicPrinter.scan(); // ❌ breaks (client never wanted this)
```

#### 🔴 Problem:

- basicPrinter is forced to implement methods (scan, fax) it doesn’t support.
- Clients depending on MachineInterface must “know” which functions are safe.
- Violates ISP.

#### ✅ Refactored with ISP (Functional Approach)

Instead of one big interface, split into smaller functional contracts:

```javascript
function Printer(machine) {
  return { print: machine.print };
}

function Scanner(machine) {
  return { scan: machine.scan };
}

function FaxMachine(machine) {
  return { fax: machine.fax };
}

// Concrete implementations
const basicPrinter = Printer({
  print: () => console.log("Printing..."),
});

const multiFunctionPrinter = {
  ...Printer({ print: () => console.log("Printing...") }),
  ...Scanner({ scan: () => console.log("Scanning...") }),
  ...FaxMachine({ fax: () => console.log("Faxing...") }),
};

// Usage
basicPrinter.print(); // ✅ works, no unused methods
multiFunctionPrinter.scan(); // ✅ works
```

#### 🔵 Benefits:

- Clients use only what they need.
- basicPrinter doesn’t pretend to “scan” or “fax”.
- multiFunctionPrinter composes interfaces without bloating.

#### 🏗 Rule of Thumb

- Favor small, focused contracts over one giant “god” interface.
- Let clients depend only on what they actually need.
- In functional JS → think in small reusable functions instead of “one do-everything function”.

#### ✅ Real-World JavaScript Examples

## 1. React Hooks

React splits concerns into small hooks instead of one huge useEverything().

```javascript
const [state, setState] = useState(0); // state handling
const [ref] = useRef(); // refs
const [data, setData] = useContext(AppContext); // context
```

- Each hook has a single responsibility.
- You don’t import unused stuff.
- ✅ ISP in practice.

#### 2. Express Middleware

Express doesn’t force one “super middleware” with log + auth + validate.
Instead, it lets you use only what you need:

```javascript
app.use(loggingMiddleware);
app.use(authMiddleware);
app.use(validationMiddleware);
```

- Each middleware is a small interface.
- Clients compose only the needed ones.
- ✅ ISP respected.

#### 3. Array Methods

Instead of one giant arrayManipulate(arr, options), JS gives you smaller interfaces:

```javascript
arr.map(fn);
arr.filter(fn);
arr.reduce(fn);
```

- Each method is focused and independent.
- Clients use only what they need.
- ✅ ISP in design.

⚡ So in functional JavaScript → ISP = small, composable, single-purpose functions/contracts that clients can pick and mix.
