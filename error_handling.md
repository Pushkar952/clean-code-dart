## **Error Handling**

Modern languages, including Dart, support exception throwing and catching.

Dart uses exceptions when an error occurs in your program. The following best practices apply to catching and throwing exceptions.


### AVOID catches without on clauses.
> Linter rule: avoid_catches_without_on_clauses

A catch clause with no on qualifier catches anything thrown by the code in the try block. Pokémon exception handling is very likely not what you want. Does your code correctly handle StackOverflowError or OutOfMemoryError? If you incorrectly pass the wrong argument to a method in that try block do you want to have your debugger point you to the mistake or would you rather that helpful ArgumentError get swallowed? Do you want any assert() statements inside that code to effectively vanish since you’re catching the thrown AssertionErrors?

The answer is probably “no”, in which case you should filter the types you catch. In most cases, you should have an on clause that limits you to the kinds of runtime failures you are aware of and are correctly handling.

In rare cases, you may wish to catch any runtime error. This is usually in framework or low-level code that tries to insulate arbitrary application code from causing problems. Even here, it is usually better to catch Exception than to catch all types. Exception is the base class for all runtime errors and excludes errors that indicate programmatic bugs in the code.
</br>
</br>

### DON’T discard errors from catches without on clauses.
If you really do feel you need to catch everything that can be thrown from a region of code, do something with what you catch. Log it, display it to the user or rethrow it, but do not silently discard it.
</br>

</br>

### DO throw objects that implement Error only for programmatic errors.
The Error class is the base class for programmatic errors. When an object of that type or one of its subinterfaces like ArgumentError is thrown, it means there is a bug in your code. When your API wants to report to a caller that it is being used incorrectly throwing an Error sends that signal clearly.

Conversely, if the exception is some kind of runtime failure that doesn’t indicate a bug in the code, then throwing an Error is misleading. Instead, throw one of the core Exception classes or some other type.
</br>
</br>

### DON’T explicitly catch Error or types that implement it.
>Linter rule: avoid_catching_errors

This follows from the above. Since an Error indicates a bug in your code, it should unwind the entire callstack, halt the program, and print a stack trace so you can locate and fix the bug.

Catching errors of these types breaks that process and masks the bug. Instead of adding error-handling code to deal with this exception after the fact, go back and fix the code that is causing it to be thrown in the first place.
</br>
</br>

### DO use rethrow to rethrow a caught exception.
>Linter rule: use_rethrow_when_possible

If you decide to rethrow an exception, prefer using the rethrow statement instead of throwing the same exception object using throw. rethrow preserves the original stack trace of the exception. throw on the other hand resets the stack trace to the last thrown position.

**Bad:**

```dart
try {
  somethingRisky();
} catch (e) {
  if (!canHandle(e)) throw e;
  handle(e);
}
```

**Good:**
```dart
try {
  somethingRisky();
} catch (e) {
  if (!canHandle(e)) rethrow;
  handle(e);
}
```

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

Instead of throwing a new exception, you could rethrow the catched one:

```dart
Future<void> myFunction1() async {
  try {
    await dio.get(/*some parameters*/);
  }
  on DioError catch (e) {
    // do something
    rethrow;
  }
  catch (e) {
    // do something
    rethrow;
  }
}
```