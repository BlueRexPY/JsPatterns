# Proxy Pattern

- #Structural

- Wrapper over an object that intercepts access to the object and can modify the behavior

- Solves the problem of controlling access and provide additional functionality to an object without changing its interface.

## Example 1 - Form validator

```javascript
class FormValidator {
  constructor(initialData = {}, validationRules = {}) {
    this.validationRules = {
      age: {
        validate: (value) => {
          const parsedValue = Number(value);
          return !isNaN(num) && num >= 18 && num <= 120;
        },
        transform: (value) => Number(value),
        errorMessage: "Age must be between 18 and 120",
      },
    };

    return new Proxy(initialData, {
      set: (target, property, value) => {
        const rule = this.validationRules[property];

        if (rule) {
          const transformedValue = rule.transform(value);

          if (!rule.validate(transformedValue))
            throw new Error(rule.errorMessage);

          target[property] = transformedValue;
        } else {
          target[property] = value;
        }

        return true;
      },

      get: (target, property) => Reflect.get(target, property),
    });
  }
}

const userForm = new FormValidator();

userForm.age = "25"; // -> 25 (number)

userForm.age = "15"; // -> Error: Age must be between 18 and 120
```

## Example 2 - Server/Client environment guard

```javascript
const env = {
  dbHost: "localhost",
  dbPort: 5432,
  dbUser: "root",
  dbPassword: "root",
  publicAnalyticsKey: "123456",
  publicPaymentKey: "654321",
};

const envProxy = new Proxy(env, {
  get: (target, property) => {
    const isClient = typeof window !== "undefined";

    if (isClient && !property.startsWith("public")) return undefined;

    return Reflect.get(target, property);
  },
  set: () => void 0,
});

// in server environment
const dbHost = envProxy.dbHost; // -> "localhost"
const publicAnalyticsKey = envProxy.publicAnalyticsKey; // -> "123456"

// in client environment
const dbHost = envProxy.dbHost; // -> "undefined"
const publicAnalyticsKey = envProxy.publicAnalyticsKey; // -> "123456"
```

## Rating

- #Meh: Situational use case, due to the low prevalence of proxies, such behavior may not be obvious, the same logic can be implemented through methods as `validator.setField("key",value)`, it will be more readable and understandable for other developers and provide more flexibility.

## Additional Resources

- [Refactoring Guru](https://refactoring.guru/design-patterns/proxy)
- [patterns.dev](https://www.patterns.dev/vanilla/proxy-pattern)
- [oodesign](https://www.oodesign.com/proxy-pattern)
