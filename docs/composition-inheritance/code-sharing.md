# Code Sharing

## Delegates

Delegates allow a class to specify that another class's implementation
of a given interface should be used to implement that interface. This
allows the programmer to favor using composition instead of
interheritance, while at the same time providing a convenient way of
reusing an existing implementation.

Here's an example to make some of these ideas concrete:

```pony
interface Dog
  fun bark(): String
  fun eat(): String
  fun growl(): String
  fun who_is_a_good_dog(): String

class RegularDog is Dog
  let _name: String
  new create(name: String) => _name = name
  fun bark(): String => "woof"
  fun eat(): String => "slurp"
  fun growl(): String => "grrrrr"
  fun who_is_a_good_dog(): String => _name

// you can't do this:
// class PoliceDog is RegularDog

class PoliceDogWithoutDelegate is Dog
  let _regular_dog: Dog
  let _rank: String

  new create(name: String, rank: String) =>
    _regular_dog = RegularDog(name)
    _rank = rank
    
  fun bark(): String => _regular_dog.bark()
  fun eat(): String => _regular_dog.eat()
  fun growl(): String => _regular_dog.growl()
  fun who_is_a_good_dog(): String => _regular_dog.who_is_a_good_dog()
  fun attack(): String => "[tackle, bite]"

class PoliceDogWithDelegate is Dog
  let _regular_dog: Dog delegate Dog
  let _rank: String
  new create(name: String, rank: String) =>
    _regular_dog = RegularDog(name)
    _rank = rank
  fun attack(): String => "[tackle, bite]"

```

The `Dog` interface specifies a few methods that all `Dog` classes
will implement. The `RegularDog` class provides an implementation of
the `Dog` interface. A `PoliceDog` is like a `RegularDog`, but it also
has an `attack()` method.

In a language that allowed classes to subtype other classes and use
their implementation, a `PoliceDog` could be created by subtyping
`RegularDog`, but Pony only allows classes to subtype interfaces and
traits, so `PoliceDog is RegularDog` is not allowed. One way of
getting around this would be to make `PoliceDog` implement the `Dog`
interface and store an instance of a `RegularDog` object, then
implement methods that pass calls on to the internal `RegularDog`
object. This is what is being done in the `PoliceDogWithoutDelegate`
class.

Creating a delegate is a way of saying, "For this particular interface
that my class implements, please use the implementation from this
other class." This is what `PoliceDogWithDelegate` is doing. It
declares a field called `_regular_dog`, which is a delegate field
because of the use of the keyword `delegate`. The delegate field has a
`RegularDog` object assigned to it in the constructor, and all
incoming function calls to the `PoliceDogWithDelegate` object for
methods in the `Dog` interface will be handled by the `RegularDog`
object aliased to `_regular_dog`.
