# Factory Pattern

- #Structural

- A function that uses props to build a new format of data

- Solves the problem of creating objects without specifying their exact classes, promoting loose coupling and flexibility. It centralizes object creation logic, simplifying code maintenance and enabling easier scalability.

## Example 1 - Cache key creation

```javascript
const getCacheKey = ({ scope, action, key }) => `${scope}:${action}:${key}`;

const cacheKey = getCacheKey({
  scope: "account",
  action: "getUserInfo",
  key: userID,
}); // -> "account:getUserInfo:0000"
```

### Example 2 - Case adaptor

```javascript
const snakeCaseToCamelCase = (obj) => {
  if (obj !== null && typeof obj === "object") {
    const newObj = {};

    for (const key in obj) {
      if (obj.hasOwnProperty(key)) {
        const camelKey = key.replace(/_([a-z])/g, (match, letter) =>
          letter.toUpperCase()
        );

        newObj[camelKey] = snakeCaseToCamelCase(obj[key]);
      }
    }
    return newObj;
  }

  return obj;
};

const snakeCaseResponse = {
  user_id: 1,
  user_name: "John",
  user_age: 25,
  nested_object: {
    nested_key: "nested_value",
  },
};

const camelCasResponse = snakeCaseToCamelCase(pythonServerResponse);
//->
// {
//   userId: 1,
//   userName: "John",
//   userAge: 25,
//   nestedObject: {
//     nestedKey: "nested_value",
//   },
// },
```

### Rating

- #MustHave: can be used in many different scenarios, from data transformation to object creation.
