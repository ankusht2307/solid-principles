## Dependency Inversion Principle (DIP)

## 📌 What is DIP?

👉 High-level modules should not depend on low-level modules. Both should depend on abstractions.
👉 Abstractions should not depend on details. Details should depend on abstractions.

In plain words:

- Don’t let your business logic (high-level) depend directly on technical details (DB, API, framework).
- Instead, both depend on an interface / contract / abstraction.
- This makes code flexible, testable, and decoupled.

#### 🚫 Example (Violation of DIP)

```javascript
class MySQLDatabase {
  save(data) {
    console.log("Saving to MySQL:", data);
  }
}

class UserService {
  constructor() {
    this.database = new MySQLDatabase(); // direct dependency ❌
  }

  createUser(user) {
    this.database.save(user);
  }
}

const service = new UserService();
service.createUser({ name: "Ankush" });
```

#### 🔴 Problem:

- UserService is tightly coupled to MySQLDatabase.
- If we switch to MongoDB or a mock for testing → we must change UserService.

#### ✅ Refactored with DIP

Introduce an abstraction (contract/strategy) for persistence:

```javascript
// Abstraction (contract)
class Database {
  save(data) {
    throw new Error("Not implemented");
  }
}

// Low-level modules (implement the contract)
class MySQLDatabase extends Database {
  save(data) {
    console.log("Saving to MySQL:", data);
  }
}

class MongoDBDatabase extends Database {
  save(data) {
    console.log("Saving to MongoDB:", data);
  }
}

// High-level module (depends on abstraction, not concrete)
class UserService {
  constructor(database) {
    this.database = database; // injected
  }

  createUser(user) {
    this.database.save(user);
  }
}

// Usage
const service1 = new UserService(new MySQLDatabase());
service1.createUser({ name: "Ankush" });

const service2 = new UserService(new MongoDBDatabase());
service2.createUser({ name: "Ankush" });
```

#### 🔵 Benefits:

- UserService doesn’t care how data is saved.
- We can swap MySQL/MongoDB/Mock without touching UserService.
- ✅ High-level (business logic) depends only on abstraction.

#### ✅ DIP in Functional JavaScript

We can achieve the same with functions as dependencies:

```javascript
function createUserService(saveFn) {
  return {
    createUser(user) {
      saveFn(user);
    },
  };
}

// Different implementations
const saveToMySQL = (user) => console.log("Saving to MySQL:", user);
const saveToMongo = (user) => console.log("Saving to MongoDB:", user);

// Usage
const userService1 = createUserService(saveToMySQL);
userService1.createUser({ name: "Ankush" });

const userService2 = createUserService(saveToMongo);
userService2.createUser({ name: "Ankush" });
```

Here:

- createUserService = high-level module.
- saveToMySQL / saveToMongo = low-level details.
- ✅ Both depend on abstraction: a saveFn contract (a function).

## 🏗 Real-World Examples of DIP in JS

#### 1. React with Dependency Injection

```javascript
function UserList({ fetchUsers }) {
  const [users, setUsers] = React.useState([]);

  React.useEffect(() => {
    fetchUsers().then(setUsers);
  }, [fetchUsers]);

  return (
    <ul>
      {users.map((u) => (
        <li key={u.id}>{u.name}</li>
      ))}
    </ul>
  );
}

// Different implementations
const apiFetchUsers = () => fetch("/api/users").then((r) => r.json());
const mockFetchUsers = () => Promise.resolve([{ id: 1, name: "Test User" }]);

<UserList fetchUsers={apiFetchUsers} />;
<UserList fetchUsers={mockFetchUsers} />;
```

✅ UI component doesn’t depend on API details — only on fetchUsers contract.

#### 2. Express.js Middleware

Express routes depend on req/res contracts, not specific servers.

```javascript
app.get("/users", (req, res) => {
  res.json({ id: 1, name: "Ankush" });
});
```

- Express provides req, res abstractions.
- Whether it’s HTTP, HTTPS, or a test mock server → route handler stays the same.

#### 3. Array Methods

```javascript
   [1, 2, 3].map(x => x \* 2);
   [1, 2, 3].map(x => x.toString());
```

- map depends on an abstraction: a callback (item) => result.
- Doesn’t care about implementation details of the callback.
- ✅ DIP built into functional programming.

#### ✅ Rule of Thumb

- High-level code = business rules, workflows.
- Low-level code = DB, API, frameworks, libraries.
- Connect them via abstractions (interfaces, callbacks, dependency injection).

⚡ And that’s Dependency Inversion Principle: keep business logic stable, swap technical details freely.
