## **Error Handling**

Modern languages, including Dart, support exception throwing and catching.

### Don't ignore errors

Example

**Bad:**

```dart
try {
  functionThrowsError();
} catch (error) {
  print(error);
}
```

**Good:**

```dart
try {
  functionThrowsError();
} catch (error) {
  // One option (more noisy than console.log):
  print(error);
  // Another option:
  notifyUserOfError(error);
  // Another option:
  reportErrorToService(error);
  // OR do all three!
}

```

### Rejected Futures

Example:

**Bad:**

```dart
getSomething()
  .then(data => {
    functionThatThrowError(data);
  })
  .catch(error => {
    print(error);
  });
```

**Good:**

```dart
getSomething()
  .then(data => {
    functionThatThrowError(data);
  })
  .catch(error => {
    // One option (more noisy than console.log):
    print(error);
    // Another option:
    notifyUserOfError(error);
    // Another option:
    reportErrorToService(error);
    // OR do all three!
  });

```
