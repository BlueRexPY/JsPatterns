# Singleton

- #Creational

- Globally available instance of a class, which can be created only once and reused multiple times.

- Prevents multiple instances of a class from being created, which can be useful for managing global state and ensuring a single instance of critical resources.

- Be Careful: May be anti-Pattern if used incorrectly, can lead to tight coupling and global state, which can make your code unresuable and hard to test. Consider using [Dependency Injection](/Vanilla/Creational/DependencyInjection.md) or [Provider Pattern](/Vanilla/Creational/Provider.md) instead.

## Example 1 - Logger via Object Literal (in most of the cases this is enough)

```javascript
const Logger = {
  logs: [],
  log(message) {
    this.logs.push(message);
    console.log(`[LOG] ${message}`);
  },
};

Logger.log("Hello, World!");
Logger.logs; // -> ["Hello, World!"]
```

## Example 2 - Logger via [Closure](/Vanilla/Behavioral/Closure.md)

```javascript
const Logger = (() => {
  let instance;

  const createInstance = () => ({
    logs: [],
    log(message) {
      this.logs.push(message);
      console.log(`[LOG] ${message}`);
    },
  });

  return {
    getInstance: () => instance ?? (instance = createInstance()),
  };
})();

const logger1 = Logger.getInstance(); // -> Created
const logger1 = Logger.getInstance(); // -> Reused

logger1.log("Hello, World!");
logger2.logs; // -> ["Hello, World!"]
```

## Example 3 - Logger via Class

```javascript
class Logger {
  constructor() {
    if (Logger.instance) return Logger.instance;

    Logger.instance = this;
    this.logs = [];
    return this;
  }

  log(message) {
    this.logs.push(message);
    console.log(`[LOG] ${message}`);
  }
}

const logger1 = new Logger(); // -> Created
const logger2 = new Logger(); // -> Reused

logger1.log("Hello, World!");
logger2.logs; // -> ["Hello, World!"]
```

## Example 4 - Thread-Safe Logger via ES6 Class

```javascript
class Logger {
  static #instance;

  constructor() {
    if (Logger.#instance) return Logger.#instance;

    this.logs = [];
    Logger.#instance = this;
  }

  log(message) {
    this.logs.push(message);
    console.log(`[LOG] ${message}`);
  }

  getLogs() {
    return [...this.logs];
  }

  static getInstance() {
    if (!Logger.#instance) Logger.#instance = new Logger();

    return Logger.#instance;
  }
}

const logger1 = Logger.getInstance(); // -> Created
const logger2 = Logger.getInstance(); // -> Reused

logger1.log("Hello, World!");
logger2.getLogs(); // -> ["Hello, World!"]
```

## Example 5 - Thread-Safe Logger via ES6 Class with `Symbol`

```javascript
const Logger = (() => {
  const instance = Symbol("instance");
  const Logger = class {
    constructor() {
      if (Logger[instance]) return Logger[instance];

      this.logs = [];
      Logger[instance] = this;
    }

    log(message) {
      this.logs.push(message);
      console.log(`[LOG] ${message}`);
    }

    getLogs() {
      return [...this.logs];
    }

    static getInstance() {
      if (!Logger[instance]) Logger[instance] = new Logger();

      return Logger[instance];
    }
  };

  return Logger;
})();

const logger1 = Logger.getInstance(); // -> Created
const logger2 = Logger.getInstance(); // -> Reused

logger1.log("Hello, World!");
logger2.getLogs(); // -> ["Hello, World!"]
```

## Rating

- #MustHave: in right hands, can be a powerful tool for managing global state and resources. But be careful, can lead to tight coupling and global state.

## Additional Resources

- [Refactoring Guru](https://refactoring.guru/design-patterns/singleton)
- [patterns.dev](https://www.patterns.dev/vanilla/singleton-pattern)
- [oodesign](https://www.oodesign.com/singleton-pattern)
