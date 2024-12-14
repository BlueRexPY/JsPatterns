# Closure Pattern

- #Behavioral

- A function that remembers and can access variables from its outer (enclosing) lexical scope even after the outer function has returned.

- Provides a powerful mechanism for data privacy, state preservation, and creating function factories with encapsulated data and behavior.

## Example 1 - uniqueId generator

```javascript
const uniqueId = (() => {
  let id = 0;

  return () => id++;
})();

uniqueId(); // -> 0
uniqueId(); // -> 1
uniqueId(); // -> 2
```

## Example 2 - memoization

```javascript
const memoize = (fn) => {
  const cache = new Map();

  return (...args) => {
    const key = JSON.stringify(args);

    if (!cache.has(key)) {
      cache.set(key, fn(...args));
    }

    return cache.get(key);
  };
};

const memoFibonacci = memoize(fibonacci);

memoFibonacci(10); // -> 55
memoFibonacci(10); // -> 55 (cached)
```

## Rating

- #MustHave: fundamental concept that enables powerful programming techniques like data privacy, function factories, and memoization

## Additional Resources

- [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures)
- [Eloquent JavaScript](https://eloquentjavascript.net/03_functions.html)
- [Arturo Herrero](https://arturoherrero.com/closure-design-patterns)
