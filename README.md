# clean-code-Flutter/Dart

Clean Code concepts for Flutter/Dart.

## Table of Contents

1. [Introduction](#introduction)
2. [Naming](#variables)
3. [Coding Patters](#functions)
4. [SOLID](#solid)
5. [Conventions] (#conventions)
6. [Formatting](#fomatting)
7. [Testing](#testing)
8. [Pacakages And Plugin](#packages)
9. [Error Handling](#error-handling)
10. [Comments](#comments)

## Introduction

![Clean code is the one that is easy to understand and most importantly easy to change.](https://media.geeksforgeeks.org/wp-content/cdn-uploads/20191216192939/Ratio-of-Time-Spent-Reading-Code-Versus-Writing.png)

Software engineering principles, from Robert C. Martin's book
[_Clean Code_](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882),
adapted for Dart. This is not a style guide. It's a guide to producing
[readable, reusable, and refactorable](https://github.com/ryanmcdermott/3rs-of-software-architecture) software in JavaScript.

Not every principle herein has to be strictly followed, and even fewer will be
universally agreed upon. These are guidelines and nothing more, but they are
ones codified over many years of collective experience by the authors of
_Clean Code_.

Our craft of software engineering is just a bit over 50 years old, and we are
still learning a lot. When software architecture is as old as architecture
itself, maybe then we will have harder rules to follow. For now, let these
guidelines serve as a touchstone by which to assess the quality of the
JavaScript code that you and your team produce.

One more thing: knowing these won't immediately make you a better software
developer, and working with them for many years doesn't mean you won't make
mistakes. Every piece of code starts as a first draft, like wet clay getting
shaped into its final form. Finally, we chisel away the imperfections when
we review it with our peers. Don't beat yourself up for first drafts that need
improvement. Beat up the code instead!

## **Variable**

### Begin global constant names with prefix "k"

**Examples:**

```dart
const double kParagraphSpacing = 1.5;
const String kSaveButtonTitle = 'Save';
const Color _kBarrierColor = Colors.black54;
```

However, where possible avoid global constants. Rather than kDefaultButtonColor, consider Button.defaultColor. If necessary, consider creating a class with a private constructor to hold relevant constants. It’s not necessary to add the k prefix to non-global constants.

### Avoid abbreviations

Unless the abbreviation is more recognizable than the expansion (e.g. XML, HTTP, JSON), expand abbreviations when selecting a name for an identifier. In general, avoid one-character names unless one character is idiomatic (for example, prefer index over i, but prefer x over horizontalPosition)

### Avoid anonymous parameter names

Provide full type information and names even for parameters that are otherwise unused.

**Good:**

```dart
onTapDown: (TapDownDetails details) { print('hello!'); },
```

**Bad:**

```dart
onTapUp: (_) { print('good bye'); }, // BAD
```

### Naming rules for typedefs and function variables

When naming callbacks, use FooCallback for the typedef, onFoo for the callback argument or property, and handleFoo for the method that is called.
If you have a callback with arguments but you want to ignore the arguments, give the type and names of the arguments anyway. That way, if someone copies and pastes your code, they will not have to look up what the arguments are.
Never call a method onFoo. If a property is called onFoo it must be a function type. (For all values of "Foo".)

### Spell words in identifiers and comments correctly

Our primary source of truth for spelling is the Material Design Specification. Our secondary source of truth is dictionaries.
Avoid "cute" spellings. For example, 'colors', not 'colorz'.
Prefer US English spellings. For example, 'colorize', not 'colourise', and 'canceled', not 'cancelled'.
Prefer compound words over "cute" spellings to avoid conflicts with reserved words. For example, 'classIdentifier', not 'klass'.

### Capitalize identifiers consistent with their spelling

If a word is correctly spelled (according to our sources of truth as described in the previous section) as a single word, then it should not have any inner capitalization or spaces.
For examples, prefer toolbar, scrollbar, but appBar ('app bar' in documentation), tabBar ('tab bar' in documentation).
Similarly, prefer offstage rather than offStage.
Avoid starting class names with iOS since that would have to capitalize as Ios which is not how that is spelled. (Use "Cupertino" or "UiKit" instead.)

### Boolean Variables

Name your boolean variables in positive ways, such as "enabled" or "visible", even if the default value is true.

This is because, when you have a property or argument named "disabled" or "hidden", it leads to code such as input.disabled = false or widget.hidden = false when you’re trying to enable or show the widget, which is very confusing.

### Variables and methods for debugging

If you have variables or methods (or even classes!) that are only used in debug mode, prefix their names with debug or \_debug (or, for classes, \_Debug).
Do not use debugging variables or methods (or classes) in the production code.

**[⬆ back to top](#table-of-contents)**

## **Coding Patters**

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

**[⬆ back to top](#table-of-contents)**

## **Formating**

### Constructor syntax

If you call super() in your initializer list, put a space between the constructor arguments' closing parenthesis and the colon. If there’s other things in the initializer list, align the super() call with the other arguments. Don’t call super if you have no arguments to pass up to the superclass.

````dart
// one-line constructor example
abstract class Foo extends StatelessWidget {
  Foo(this.bar, { Key key, this.child }) : super(key: key);
  final int bar;
  final Widget child;
  // ...
}
```dart
// fully expanded constructor example
abstract class Foo extends StatelessWidget {
  Foo(
    this.bar, {
    Key key,
    Widget childWidget,
  }) : child = childWidget,
       super(
         key: key,
       );
  final int bar;
  final Widget child;
  // ...
}
````

### Max line length of 80 characters

Aim for a maximum line length of roughly 80 characters, but prefer going over if breaking the line would make it less readable, or if it would make the line less consistent with other nearby lines. Prefer avoiding line breaks after assignment operators.

**Bad:**

```dart
final int a = 1;
final int b = 2;
final int c =
    a.very.very.very.very.very.long.expression.that.returns.three.eventually().but.is.very.long();


```

**Good:**

```dart
// BETTER (consistent lines, not much longer than the earlier example)
final int a = 1;
final int b = 2;
```

**Bad:**

```dart
// BAD (breaks after assignment operator)
final List<FooBarBaz> _members =
  <FooBarBaz>[const Quux(), const Qaax(), const Qeex()];
```

**Better:**

```dart
// BETTER (only slightly goes over 80 chars)
final List<FooBarBaz> _members = <FooBarBaz>[const Quux(), const Qaax(), const Qeex()];
```

**Good:**

```dart
//(fits in 80 chars)
final List<FooBarBaz> _members = <FooBarBaz>[
  const Quux(),
  const Qaax(),
  const Qeex(),
];
```

### Indent multi-line argument

When breaking an argument list into multiple lines, indent the arguments two characters from the previous line.

Example:

```dart
Foo f = Foo(
  bar: 1.0,
  quux: 2.0,
);
```

### Trailing comma

Use a trailing comma for arguments, parameters, and list items, but only if they each have their own line.

Example:

**Bad:**

```dart
foo1(
  bar,
  baz,
);
```

**Good:**

```dart
foo2(bar, baz);
```

### Quotes

Use double quotes for nested strings or (optionally) for strings that contain single quotes. For all other strings, use single quotes.
Example:

```dart
print('Hello ${name.split(" ")[0]}');
```

### Short functions and Methods

Consider using ⇒ for short functions and methods.
But only use ⇒ when everything, including the function declaration, fits on a single line.

Example:
**Bad:**

```dart
String capitalize(String s) =>
  '${s[0].toUpperCase()}${s.substring(1)}';
```

**Good:**

```dart
String capitalize(String s) => '${s[0].toUpperCase()}${s.substring(1)}';

```

### ⇒ for inline callbacks

Example:

```dart
// GOOD, but slightly more verbose than necessary since it doesn't use =>
  @override
  Widget build(BuildContext context) {
    return PopupMenuButton<String>(
      onSelected: (String value) { print('Selected: $value'); },
      itemBuilder: (BuildContext context) {
        return <PopupMenuItem<String>>[
          PopupMenuItem<String>(
            value: 'Friends',
            child: MenuItemWithIcon(Icons.people, 'Friends', '5 new')
          ),
          PopupMenuItem<String>(
            value: 'Events',
            child: MenuItemWithIcon(Icons.event, 'Events', '12 upcoming')
          ),
        ];
      }
    );
  }
```

**Good:**

```dart
// GOOD, does use =>, slightly briefer
  @override
  Widget build(BuildContext context) {
    return PopupMenuButton<String>(
      onSelected: (String value) { print('Selected: $value'); },
      itemBuilder: (BuildContext context) => <PopupMenuItem<String>>[
        PopupMenuItem<String>(
          value: 'Friends',
          child: MenuItemWithIcon(Icons.people, 'Friends', '5 new')
        ),
        PopupMenuItem<String>(
          value: 'Events',
          child: MenuItemWithIcon(Icons.event, 'Events', '12 upcoming')
        ),
      ]
    );
  }
```

The important part is that the closing punctuation lines up with the start of the line that has the opening punctuation so that you can easily determine what’s going on by just scanning the indentation on the left edge.

### single line for short collection-if and collection-for

If the code fits in a single line don’t split it.

**Bad:**

```dart
final List<String> args = <String>[
  'test',
  if (useFlutterTestFormatter) '-rjson'
  else '-rcompact',
  '-j1',
  if (!hasColor)
    '--no-color',
  for (final String opt in others)
    opt,
];
```

**Good:**

```dart
final List<String> args = <String>[
  'test',
  if (useFlutterTestFormatter) '-rjson' else '-rcompact',
  '-j1',
  if (!hasColor) '--no-color',
  for (final String opt in others) opt,
];


```

### Spread inside collection-if or collection-for on the same line

**Bad:**

```dart
final List<String> args = <String>[
  'test',
  if (condA)
    ...<String>[
      'b',
      'c',
    ]
  else
    '-rcompact',
  for (final String opt in others)
    ...<String>[
      m1(opt),
      m2(opt),
    ],
];
```

**Good:**

```dart
final List<String> args = <String>[
  'test',
  if (condA) ...<String>[
    'b',
    'c',
  ] else
    '-rcompact',
  for (final String opt in others) ...<String>[
    m1(opt),
    m2(opt),
  ],
];
```

### Align Expressions

Where possible, subexpressions on different lines should be aligned, to make the structure of the expression easier. When doing this with a return statement chaining || or && operators, consider putting the operators on the left-hand side instead of the right-hand side.

**Bad:**

```dart
if (foo.foo.foo + bar.bar.bar * baz - foo.foo.foo * 2 +
    bar.bar.bar * 2 * baz > foo.foo.foo) {
  // ...
}
```

**Good:**

```dart
if (bar.bar.bar * 3 * baz > foo.foo.foo * 2) {
  // ...
}
```

**[⬆ back to top](#table-of-contents)**
