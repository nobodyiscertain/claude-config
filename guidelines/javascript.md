# JavaScript Guidelines

Comprehensive conventions and style guidelines for JavaScript development.

## Core Principles

- **Check existing setup first**: Always examine package.json and build config to understand the stack
- **Default to vanilla JavaScript**: Unless the project specifically uses a framework
- **Prefer simplicity**: Use built-in browser APIs and standard JavaScript before adding libraries
- **Progressive enhancement**: Start with working HTML, enhance with JavaScript

---

## Framework Detection

### Before Writing Code
1. Check `package.json` for existing dependencies
2. Look for framework-specific files:
   - React: `jsx/tsx` files, `react` in dependencies
   - Vue: `.vue` files, `vue` in dependencies
   - Svelte: `.svelte` files
   - Next.js: `next.config.js`
   - Vite: `vite.config.js`
3. Check build tools: Webpack, Vite, esbuild, Parcel
4. Understand if TypeScript is used (`.ts/.tsx` files)

### Rails-Specific JavaScript
For Rails applications, prefer:
1. **Hotwire (Turbo + Stimulus)** for modern Rails apps
2. **Importmaps** or **esbuild** for asset bundling
3. Avoid heavy JavaScript frameworks unless already in use

---

## Vanilla JavaScript

### Modern JavaScript Features
```javascript
// Use const/let, not var
const API_URL = 'https://api.example.com';
let counter = 0;

// Arrow functions
const double = (n) => n * 2;

// Destructuring
const { name, email } = user;
const [first, second] = items;

// Template literals
const message = `Hello, ${name}!`;

// Spread operator
const newArray = [...oldArray, newItem];
const newObject = { ...oldObject, updatedField: 'value' };

// Optional chaining
const city = user?.address?.city;

// Nullish coalescing
const name = user.name ?? 'Anonymous';

// Array methods
const doubled = numbers.map(n => n * 2);
const evens = numbers.filter(n => n % 2 === 0);
const sum = numbers.reduce((acc, n) => acc + n, 0);
```

### DOM Manipulation
```javascript
// Query selectors
const element = document.querySelector('.my-class');
const elements = document.querySelectorAll('.my-class');

// Event listeners
element.addEventListener('click', (event) => {
  event.preventDefault();
  // Handle click
});

// Creating elements
const div = document.createElement('div');
div.className = 'card';
div.textContent = 'Hello';
document.body.appendChild(div);

// Updating content
element.textContent = 'New text';
element.innerHTML = '<strong>Bold text</strong>';  // Be careful with XSS

// Classes
element.classList.add('active');
element.classList.remove('hidden');
element.classList.toggle('expanded');

// Attributes
element.setAttribute('data-id', '123');
const id = element.getAttribute('data-id');
element.dataset.id = '123';  // Preferred for data attributes
```

### Async/Await
```javascript
// Prefer async/await over promises
async function fetchUser(id) {
  try {
    const response = await fetch(`/api/users/${id}`);
    if (!response.ok) throw new Error('Failed to fetch');
    const user = await response.json();
    return user;
  } catch (error) {
    console.error('Error fetching user:', error);
    throw error;
  }
}

// Using the async function
async function loadUser() {
  const user = await fetchUser(123);
  displayUser(user);
}
```

### Fetch API
```javascript
// GET request
const response = await fetch('/api/articles');
const articles = await response.json();

// POST request
const response = await fetch('/api/articles', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'X-CSRF-Token': getCsrfToken()  // For Rails
  },
  body: JSON.stringify({
    title: 'New Article',
    body: 'Content here'
  })
});

// Error handling
if (!response.ok) {
  throw new Error(`HTTP error! status: ${response.status}`);
}
```

---

## Hotwire / Stimulus (Rails)

### Stimulus Controllers
```javascript
// app/javascript/controllers/hello_controller.js
import { Controller } from "@hotwired/stimulus"

export default class extends Controller {
  // Targets
  static targets = ["name", "output"]

  // Values (typed properties)
  static values = {
    name: String,
    count: { type: Number, default: 0 }
  }

  // Actions
  greet(event) {
    event.preventDefault()
    this.outputTarget.textContent = `Hello, ${this.nameTarget.value}!`
  }

  increment() {
    this.countValue++
  }

  // Lifecycle callbacks
  connect() {
    console.log("Controller connected")
  }

  disconnect() {
    console.log("Controller disconnected")
  }

  // Value changed callbacks
  countValueChanged(value, previousValue) {
    console.log(`Count changed from ${previousValue} to ${value}`)
  }
}
```

```html
<!-- Usage in view -->
<div data-controller="hello">
  <input data-hello-target="name" type="text">
  <button data-action="click->hello#greet">Greet</button>
  <div data-hello-target="output"></div>
</div>
```

### Turbo Frames
```html
<!-- Scoped updates without full page reload -->
<turbo-frame id="messages">
  <%= render @messages %>
  <%= link_to "Load more", messages_path, data: { turbo_frame: "messages" } %>
</turbo-frame>
```

### Turbo Streams
```ruby
# Controller
def create
  @message = Message.create(message_params)

  respond_to do |format|
    format.turbo_stream  # Renders create.turbo_stream.erb
    format.html { redirect_to messages_path }
  end
end
```

```erb
<!-- create.turbo_stream.erb -->
<turbo-stream action="append" target="messages">
  <template>
    <%= render @message %>
  </template>
</turbo-stream>
```

---

## React (if project uses it)

### Functional Components with Hooks
```javascript
import { useState, useEffect } from 'react';

function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    async function fetchUser() {
      try {
        const response = await fetch(`/api/users/${userId}`);
        const data = await response.json();
        setUser(data);
      } catch (err) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    }

    fetchUser();
  }, [userId]);  // Re-run when userId changes

  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;

  return (
    <div>
      <h1>{user.name}</h1>
      <p>{user.email}</p>
    </div>
  );
}
```

### Component Patterns
```javascript
// Props destructuring
function Button({ onClick, children, variant = 'primary' }) {
  return (
    <button
      className={`btn btn-${variant}`}
      onClick={onClick}
    >
      {children}
    </button>
  );
}

// Conditional rendering
function UserGreeting({ user }) {
  return user ? (
    <h1>Welcome back, {user.name}!</h1>
  ) : (
    <h1>Please sign in.</h1>
  );
}

// List rendering
function ArticleList({ articles }) {
  return (
    <ul>
      {articles.map(article => (
        <li key={article.id}>
          <Article article={article} />
        </li>
      ))}
    </ul>
  );
}
```

---

## TypeScript

### When to Use TypeScript
- **DO use** if project already uses TypeScript
- **DON'T introduce** TypeScript unless explicitly requested
- Check for `tsconfig.json` to confirm TypeScript usage

### Basic TypeScript Patterns
```typescript
// Type annotations
const name: string = 'John';
const age: number = 30;
const isActive: boolean = true;

// Interfaces
interface User {
  id: number;
  name: string;
  email: string;
  role?: string;  // Optional property
}

// Function types
function greetUser(user: User): string {
  return `Hello, ${user.name}!`;
}

// Async functions
async function fetchUser(id: number): Promise<User> {
  const response = await fetch(`/api/users/${id}`);
  return response.json();
}

// Type guards
function isUser(obj: any): obj is User {
  return 'id' in obj && 'name' in obj && 'email' in obj;
}
```

---

## Code Style and Conventions

### Naming Conventions
```javascript
// camelCase for variables and functions
const userName = 'John';
function getUserName() { }

// PascalCase for classes and components
class UserManager { }
function UserProfile() { }

// SCREAMING_SNAKE_CASE for constants
const API_BASE_URL = 'https://api.example.com';
const MAX_RETRIES = 3;

// Descriptive names
// Bad
const d = new Date();
const fn = (x) => x * 2;

// Good
const currentDate = new Date();
const doubleNumber = (number) => number * 2;
```

### Function Structure
```javascript
// Keep functions small and focused
// Bad - doing too much
function processUser(user) {
  validateUser(user);
  saveToDatabase(user);
  sendEmail(user);
  updateCache(user);
  logActivity(user);
}

// Good - single responsibility
function saveUser(user) {
  validateUser(user);
  return database.save(user);
}

function notifyUser(user) {
  return emailService.send(user);
}
```

### Error Handling
```javascript
// Always handle errors
async function saveArticle(article) {
  try {
    const response = await fetch('/api/articles', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(article)
    });

    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }

    return await response.json();
  } catch (error) {
    console.error('Failed to save article:', error);
    // Re-throw or handle appropriately
    throw new Error('Failed to save article. Please try again.');
  }
}
```

### Comments
```javascript
// Explain WHY, not WHAT
// Bad
// Increment counter by 1
counter++;

// Good
// Reset attempt counter after successful save to prevent rate limiting
attemptCounter = 0;

// Use JSDoc for function documentation
/**
 * Fetches user data from the API
 * @param {number} userId - The user's ID
 * @returns {Promise<User>} The user object
 * @throws {Error} If the user is not found
 */
async function fetchUser(userId) {
  // Implementation
}
```

---

## Testing

### General Testing Guidelines
See [testing.md](testing.md) for comprehensive testing guidance.

### JavaScript-Specific Testing
```javascript
// Jest/Vitest example
describe('UserService', () => {
  test('fetches user by id', async () => {
    const user = await UserService.fetch(123);
    expect(user).toBeDefined();
    expect(user.id).toBe(123);
  });

  test('throws error for invalid id', async () => {
    await expect(UserService.fetch(-1)).rejects.toThrow();
  });
});

// For Hotwire/Stimulus: Use Rails system tests
# test/system/comments_test.rb
test "adding a comment" do
  visit article_path(@article)
  fill_in "Comment", with: "Great article!"
  click_button "Post Comment"

  assert_text "Great article!"
end
```

---

## Security

### XSS Prevention
```javascript
// Bad - vulnerable to XSS
element.innerHTML = userInput;

// Good - use textContent
element.textContent = userInput;

// If HTML is needed, sanitize first
import DOMPurify from 'dompurify';
element.innerHTML = DOMPurify.sanitize(userInput);
```

### CSRF Protection (Rails)
```javascript
// Get CSRF token for fetch requests
function getCsrfToken() {
  return document.querySelector('meta[name="csrf-token"]')?.content;
}

// Include in requests
fetch('/api/articles', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'X-CSRF-Token': getCsrfToken()
  },
  body: JSON.stringify(data)
});
```

### Input Validation
```javascript
// Always validate user input
function validateEmail(email) {
  const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
  return emailRegex.test(email);
}

function sanitizeInput(input) {
  return input.trim().slice(0, 200);  // Trim and limit length
}
```

---

## Performance

### Debouncing and Throttling
```javascript
// Debounce - wait for user to stop typing
function debounce(func, delay) {
  let timeoutId;
  return function (...args) {
    clearTimeout(timeoutId);
    timeoutId = setTimeout(() => func.apply(this, args), delay);
  };
}

// Usage
const searchInput = document.querySelector('#search');
searchInput.addEventListener('input', debounce((e) => {
  performSearch(e.target.value);
}, 300));

// Throttle - limit execution rate
function throttle(func, limit) {
  let inThrottle;
  return function (...args) {
    if (!inThrottle) {
      func.apply(this, args);
      inThrottle = true;
      setTimeout(() => inThrottle = false, limit);
    }
  };
}
```

### Lazy Loading
```javascript
// Intersection Observer for lazy loading
const observer = new IntersectionObserver((entries) => {
  entries.forEach(entry => {
    if (entry.isIntersecting) {
      const img = entry.target;
      img.src = img.dataset.src;
      observer.unobserve(img);
    }
  });
});

document.querySelectorAll('img[data-src]').forEach(img => {
  observer.observe(img);
});
```

### Memory Management
```javascript
// Clean up event listeners
class Component {
  constructor() {
    this.handleClick = this.handleClick.bind(this);
  }

  mount() {
    this.button.addEventListener('click', this.handleClick);
  }

  unmount() {
    this.button.removeEventListener('click', this.handleClick);
  }
}
```

---

## Build Tools and Module Systems

### ES Modules
```javascript
// Exporting
export const API_URL = 'https://api.example.com';
export function fetchUser(id) { }
export default class UserService { }

// Importing
import UserService from './user-service.js';
import { fetchUser, API_URL } from './api.js';
```

### Rails Asset Pipeline
```javascript
// With importmaps (modern Rails)
// application.js
import "@hotwired/turbo-rails"
import "./controllers"

// With esbuild
// app/javascript/application.js
import * as bootstrap from "bootstrap"
```

---

## Common Patterns

### Module Pattern
```javascript
const UserModule = (() => {
  // Private
  let users = [];

  function validateUser(user) {
    return user.name && user.email;
  }

  // Public API
  return {
    add(user) {
      if (validateUser(user)) {
        users.push(user);
      }
    },
    getAll() {
      return [...users];  // Return copy
    }
  };
})();
```

### Observer Pattern
```javascript
class EventEmitter {
  constructor() {
    this.events = {};
  }

  on(event, listener) {
    if (!this.events[event]) {
      this.events[event] = [];
    }
    this.events[event].push(listener);
  }

  emit(event, data) {
    if (this.events[event]) {
      this.events[event].forEach(listener => listener(data));
    }
  }

  off(event, listenerToRemove) {
    if (this.events[event]) {
      this.events[event] = this.events[event].filter(
        listener => listener !== listenerToRemove
      );
    }
  }
}
```

---

## Useful Commands

```bash
# Node/npm
npm install                  # Install dependencies
npm run dev                  # Start dev server (DON'T use npm run build)
npm run lint                 # Run linter
npm test                     # Run tests

# Rails with JavaScript
rails assets:precompile      # Precompile assets (production only)
rails assets:clobber         # Clear asset cache

# Debugging
node --inspect app.js        # Start with debugger
npm run dev -- --debug       # Run dev server in debug mode
```

---

## Anti-Patterns to Avoid

```javascript
// Bad - Global variables
var userName = 'John';  // Pollutes global scope

// Bad - Callback hell
getData((data) => {
  getMoreData(data, (moreData) => {
    getEvenMoreData(moreData, (evenMoreData) => {
      // Deep nesting
    });
  });
});

// Good - Use async/await
const data = await getData();
const moreData = await getMoreData(data);
const evenMoreData = await getEvenMoreData(moreData);

// Bad - Mutating objects
function updateUser(user) {
  user.lastSeen = Date.now();  // Mutates input
  return user;
}

// Good - Return new object
function updateUser(user) {
  return { ...user, lastSeen: Date.now() };
}

// Bad - Long parameter lists
function createUser(name, email, age, address, phone, role) { }

// Good - Use object parameter
function createUser({ name, email, age, address, phone, role }) { }
```

---

## References

- [MDN JavaScript Guide](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide)
- [JavaScript.info](https://javascript.info/)
- [Hotwire Handbook](https://hotwired.dev/)
- [React Documentation](https://react.dev/)
- [TypeScript Handbook](https://www.typescriptlang.org/docs/)
