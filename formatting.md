## **Formatting**

![formatting](https://github.com/geekruchika/clean-code-dart/blob/main/assets/formatting.png)
### Constructor syntax

If you call super() in your initializer list, put a space between the constructor arguments' closing parenthesis and the colon. If there’s other things in the initializer list, align the super() call with the other arguments. Don’t call super if you have no arguments to pass up to the superclass.

```dart
// one-line constructor example
abstract class Foo extends StatelessWidget {
  Foo(this.bar, { Key key, this.child }) : super(key: key);
  final int bar;
  final Widget child;
  // ...
}
```
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
```

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