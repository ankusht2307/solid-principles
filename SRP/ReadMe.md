## 📌 What is SRP?

The Single Responsibility Principle says:

👉 A class (or module, function, service) should have only one reason to change.

#### That means:

- Each piece of code should do one thing, and do it well.
- If you find a class handling multiple unrelated concerns, that’s a violation of SRP.

#### 🔎 Why does it matter?

- Makes code easier to read (you know exactly what a class/function is responsible for).
- Improves maintainability (when requirements change, you don’t break unrelated logic).
- Encourages reusability (a class focused on one thing can be reused elsewhere).
- Helps with testing (unit tests are simpler when the unit has a clear purpose).

#### ✅ Example (Violation of SRP)

```javascript
class User {
  constructor(name, email) {
    this.name = name;
    this.email = email;
  }

  // Responsibility 1: User data
  getUserInfo() {
    return `${this.name} <${this.email}>`;
  }

  // Responsibility 2: Validation
  validateEmail() {
    return /\S+@\S+\.\S+/.test(this.email);
  }

  // Responsibility 3: Persistence (DB)
  saveToDatabase() {
    console.log("Saving user to DB...");
  }
}
```

#### 🔴 Problem:

This User class is doing 3 jobs: handling user data, validating, and saving to DB.
It has multiple reasons to change (e.g., DB logic changes, validation rules change, user model changes).

#### ✅ Refactored with SRP

```javascript
class User {
  constructor(name, email) {
    this.name = name;
    this.email = email;
  }

  getUserInfo() {
    return `${this.name} <${this.email}>`;
  }
}

class UserValidator {
  static validateEmail(email) {
    return /\S+@\S+\.\S+/.test(email);
  }
}

class UserRepository {
  save(user) {
    console.log("Saving user to DB...");
  }
}
```

#### 🔵 Now:

- User → only holds user data.
- UserValidator → only validates.
- UserRepository → only deals with persistence.
- Each has one reason to change, and responsibilities are cleanly separated.

#### 🏗 Rule of Thumb

Ask yourself:
"If requirements change, how many reasons does this class have to be modified?"
If it’s more than one, split it.

## 📌 SRP for Functions

The same rule applies:

👉 A function should do one thing and have one reason to change.

If a function does multiple things, it becomes harder to:

- Read
- Test
- Reuse
- Maintain

#### 🚫 Bad Example (Violation of SRP)

```javascript
function registerUser(name, email) {
  // 1. Validate email
  if (!/\S+@\S+\.\S+/.test(email)) {
    throw new Error("Invalid email");
  }

  // 2. Create user object
  const user = { name, email };

  // 3. Save to DB
  console.log("Saving user to database...");

  // 4. Send welcome email
  console.log("Sending welcome email to " + email);

  return user;
}
```

#### 🔴 Problem:

This single function is doing 4 jobs: validation, object creation, persistence, notification.
It has multiple reasons to change.

#### ✅ Refactored with SRP

```javascript
function validateEmail(email) {
  return /\S+@\S+\.\S+/.test(email);
}

function createUser(name, email) {
  return { name, email };
}

function saveUser(user) {
  console.log("Saving user to database...");
}

function sendWelcomeEmail(user) {
  console.log("Sending welcome email to " + user.email);
}

// High-level function (orchestrates the flow)
function registerUser(name, email) {
  if (!validateEmail(email)) throw new Error("Invalid email");

  const user = createUser(name, email);
  saveUser(user);
  sendWelcomeEmail(user);

  return user;
}
```

#### 🔵 Benefits:

- Each function has one responsibility.
- Easy to test each piece individually.
- If tomorrow the email validation changes, only validateEmail changes, not everything else.
