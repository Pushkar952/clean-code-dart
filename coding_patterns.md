## **Coding Patterns**

### Use asserts liberally to detect contract violations and verify invariants

assert() allows us to be diligent about correctness without paying a performance penalty in release mode because Dart only evaluates asserts in debug mode.
It should be used to verify contracts and invariants are being met as we expect. Asserts do not enforce contracts since they do not run at all in release builds. They should be used in cases where it should be impossible for the condition to be false without there being a bug somewhere in the code.
The following example is from box.dart:

```dart
abstract class RenderBox extends RenderObject {
  // ...
  double getDistanceToBaseline(TextBaseline baseline, {bool onlyReal: false}) {
    // simple asserts:
    assert(!needsLayout);
    assert(!_debugDoingBaseline);
    // more complicated asserts:
    assert(() {
      final RenderObject parent = this.parent;
      if (owner.debugDoingLayout)
        return (RenderObject.debugActiveLayout == parent) &&
            parent.debugDoingThisLayout;
      if (owner.debugDoingPaint)
        return ((RenderObject.debugActivePaint == parent) &&
                parent.debugDoingThisPaint) ||
            ((RenderObject.debugActivePaint == this) && debugDoingThisPaint);
      assert(parent == this.parent);
      return false;
    });
    // ...
    return 0.0;
  }

  // ...
}
```

Example:

**Good:**

```dart
const EdgeInsets.symmetric(horizontal: 8.0);
```

**Bad:**

```dart
const EdgeInsets.TRBL(0.0, 8.0, 0.0, 8.0);
```

### Avoid using var

All variables and arguments are typed; avoid "dynamic" or "Object" in any case where you could figure out the actual type. Always specialize in generic types where possible. Explicitly type all list and map literals.
This achieves two purposes: it verifies that the type that the compiler would infer matches the type you expect, and it makes the code self-documenting in the case where the type is not obvious (e.g. when calling anything other than a constructor).
Always avoid "var". Use "dynamic" if you are being explicit that the type is unknown, but prefer "Object" and casting, as using dynamic disables all static checking.

### Avoid using the library and part of.

Prefer that each library be self-contained. Only name a library if you are documenting it (see the documentation section).
We avoid using part of because that feature makes it very hard to reason about how private a private really is, and tends to encourage "spaghetti" code (where distant components refer to each other) rather than "lasagna" code (where each section of the code is cleanly layered and separable).

### Avoid mysterious and magical numbers that lack a clear derivation

Numbers in tests and elsewhere should be clearly understandable. When the provenance of a number is not obvious, consider either leaving the expression or adding a clear comment (bonus points for leaving a diagram).

**Bad:**

```dart
expect(rect.left, 4.24264068712);
```

**Good:**

```dart
expect(rect.left, 3.0 * math.sqrt(2));
```

### Common boilerplates for operator == and hashCode

We have many classes that override operator == and hashCode ("value classes"). To keep the code consistent, we can rely on the equitable package to avoid having boilerplate code

### Override toString

Use Diagnosticable (rather than directly overriding toString) on all but the most trivial classes. That allows us to inspect the object from devtools and IDEs.
For trivial classes, override toString as follows, to aid in debugging:

```dart
@override
String toString() => '$runtimeType($bar, $baz, $quux)'
```