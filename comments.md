# **Comments**
</br>

## Avoid useless documentation
</br>
Avoid checking in such documentation, because it is no better than no documentation but will prevent us from noticing that the identifier is not actually documented.

Example:

**Bad:**

```dart
/// The background color.
final Color backgroundColor;

/// Half the diameter of the circle.
final double radius;
```

**Good:**

```dart

/// The color with which to fill the circle. Changing the background
/// color will cause the avatar to animate to the new color.
final Color backgroundColor;

/// The size of the avatar. Changing the radius will cause the
/// avatar to animate to the new size.
final double radius;
```
</br>

## Avoid empty prose
</br>
It’s easy to use more words than necessary. Avoid doing so where possible, even if the result is somewhat terse.

**Bad:**

```dart
/// Note: It is important to be aware of the fact that in the
/// absence of an explicit value, this property defaults to 2.

```

**Good:**

```dart
/// Defaults to 2.
```

In particular, avoid saying "Note:". It adds nothing.
</br>

## Leave breadcrumbs in the comments
</br>
This is especially important for documentation at the level of classes.

If a class is constructed using a builder of some sort or can be obtained via some mechanism other than merely calling the constructor, then include this information in the documentation for the class.

If a class is typically used by passing it to a particular API, then include that information in the class documentation also.

If a method is the main mechanism used to obtain a particular object or is the main way to consume a particular object, then mention that in the method’s description.

Typedefs should mention at least one place where the signature is used.

These rules result in a chain of breadcrumbs that a reader can follow to get from any class or method that they might think is relevant to their task all the way up to the class or method they actually need.

Example:

**Good:**

```dart
/// An object representing a sequence of recorded graphical operations.
///
/// To create a [Picture], use a [PictureRecorder].
///
/// A [Picture] can be placed in a [Scene] using a [SceneBuilder], via
/// the [SceneBuilder.addPicture] method. A [Picture] can also be
/// drawn into a [Canvas], using the [Canvas.drawPicture] method.
abstract class Picture ...
```

You can also use "See also" links, is in:

```dart
/// See also:
///
/// * [FooBar], which is another way to peel oranges.
/// * [Baz], which quuxes the wibble.
```

Each line should end with a period. Prefer "which…​" rather than parentheticals on such lines. There should be a blank line between "See also:" and the first item in the bulleted list.
</br>

## Refactor the code when the documentation would be incomprehensible
</br>
If writing the documentation proves to be difficult because the API is convoluted, then rewrite the API rather than trying to document it.

Canonical terminology

The documentation should use consistent terminology:

method - a member of a class that is a non-anonymous closure
function - a callable non-anonymous closure that isn’t a member of a class
parameter - a variable defined in a closure signature and possibly used in the closure body.
argument - the value passed to closure when calling it.

Prefer the term "call" to the term "invoke" when talking about jumping to a closure.

Prefer the term "member variable" to the term "instance variable" when talking about variables associated with a specific object.

Typedef dartdocs should usually start with the phrase "Signature for…​".

## Mark deprecated APIs
</br>
According to Flutter’s Design Principles, use @deprecated with a clear recommendation of what to use instead.

In some cases, using @deprecated will turn the tree red for longer than the Flutter team can accommodate. In those cases, and when we want to give developers enough time to move to the new API, you should use this format:

```dart
/// (Deprecated, use [lib.class] instead) Original one-line statement.
///
/// A longer, one-liner that explains the context for the deprecation.
///
/// The rest of the comments
```

## Use /// for public-quality private documentation
</br>
In general, private code can and should also be documented. If that documentation is of good enough quality that we could include it verbatim when making the class public (i.e. it satisfies all the style guidelines above), then you can use /// for those docs, even though they’re private.

Documentation of private APIs that is not of sufficient quality should only use //. That way, if we ever make the corresponding class public, those documentation comments will be flagged as missing, and we will know to examine them more carefully.

## Avoid checking in comments that ask questions
</br>
Find the answers to the questions, or describe the confusion, including references to where you found answers.

If commenting on a workaround due to a bug, also leave a link to the issue and a TODO to clean it up when the bug is fixed.

Example:

**Bad:**

```dart
// What should this be?
// This is a workaround.
```

**Good:**

```dart
// According to this specification, this should be 2.0, but according to that
// specification, it should be 3.0. We split the difference and went with
// 2.5, because we didn't know what else to do.


// TODO(username): Converting color to RGB because class Color doesn't support
//                 hex yet. See http://link/to/a/bug/123
```

TODOs should include the string TODO in all caps, followed by the GitHub username of the person with the best context about the problem referenced by the TODO in parenthesis. A TODO is not a commitment that the person referenced will fix the problem, it is intended to be the person with enough context to explain the problem. Thus, when you create a TODO, it is almost always your username that is given.

Including an issue link in a TODO description is required.

## Comment all // ignores
</br>
Sometimes, it is necessary to write code that the analyzer is unhappy with.

If you find yourself in this situation, consider how you got there. Is the analyzer actually correct but you don’t want to admit it? Think about how you could refactor your code so that the analyzer is happy. If such a refactor would make the code better, do it. (It might be a lot of work…​ embrace the yak shave.)

If you are really really sure that you have no choice but to silence the analyzer, use `// ignore: `. The ignore directive should be on the same line as the analyzer warning.

If the ignore is temporary (e.g. a workaround for a bug in the compiler or analyzer, or a workaround for some known problem in Flutter that you cannot fix), then add a link to the relevant bug, as follows:

```dart
foo(); // ignore: lint_code, https://link.to.bug/goes/here
```

If the ignore directive is permanent, e.g. because one of our clients has some unavoidable false positives and in this case violating the lint is definitely better than all other options, then add a comment explaining why:

```dart
foo(); // ignore: lint_code, sadly there is no choice but to do
// this because we need to twiddle the quux and the bar is zorgle.
```

## Comment empty closures to setState
</br>
Generally the closure passed to setState should include all the code that changes the state. Sometimes this is not possible because the state changed elsewhere and the setState is called in response. In those cases, include a comment in the setState closure that explains what the state is that changed.

```dart
setState(() { /* The animation ticked. We use the animation's value in the build method. */ });
```
