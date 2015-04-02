# Macros/Metaprogramming in Programming Languages

The following is a list of programming languages and their syntax for doing metaprogramming.


## C

Macros in C are just glorified string substitution.

```c
#define MAX(a,b) ((a) > (b) ? (a) : (b))


// later on in code
y = MAX(1,2);
// which is substituted into
y = 1 > 2 ? 1 : 2;
// which compilers are smart enough to simplify to
y = 2;
```


## Clojure

Clojure's macro system is pretty straight-forward. It looks and feels like a regular function definition.

#### Example 1: Incanter formula macro


See the full implementation at the [Incanter Github repo](https://github.com/incanter/incanter/blob/master/modules/incanter-core/src/incanter/infix.clj)


*Definition:*

```clojure
(defmacro formula
  "Convert from infix notation to prefix notation"
  ([& equation]
    (infix-to-prefix equation)))

```

Where `infix-to-prefix` is a regular function that converts a mathematical expression from infix to prefix notation (obeying precedence rules).

*Usage:*

```clojure
(formula 7 + 8 - 2 * 6 / 2) ;;9
```


#### Example 2: Components in Entity-Component-System

This example is take from the [gambit framework](https://github.com/ibdknox/gambit/blob/master/cljs/game/lib/macros.clj) for making games based on an entity-component-system.


*Definition:*

```clojure
(defmacro component [name params & r]
  `(defn ~name ~params
     (cljs.core/js-obj "name" ~(keyword (clojure.core/name name)) ~@r)))
```


*Usage:*

```clojure
(component vector [x y]
           :x x
           :y y)
```

Later on...

```clojure
(vector 5 10) ;; {:x 5, :y 10}
```

## Dart (Metadata)
Dart does not support macros and does metaprogramming using mirror-based reflection. That said, Dart does have a mechanism to include metadata (which can then be used by reflection to acheive the sorts of things you would achieve with macros normally).


####Example 1: Todo annotation

*Definition:*

```dart
class Todo{
  final String who;
  final String what;

  const todo(this.who, this.what);
}
```

*Usage:*


```dart
@Todo("Hassan", "Finish implementing this function")
void importantFunction(){

}
```

####Example 2 : Hello world in Angular 2 Dart

This is an example of creating a "Hello world" component in Angular2Dart.

```dart
@Component(
  selector: "app"
)

@Template(
  inline: "<p>Hello {{ value }}!</p>"
)

class AppComponent{
  String value = "World";
}
```

## Julia

Julia is basically a lisp. While it may not look like it at first, given its MATLAB-like syntax, that syntax de-sugars to basically what amounts to S-Expressions.

To use S-Expressions in Julia, you must quote them with a colon, as so: `:(1 + 2)`

This expression is then considered of having the following type: `Expr`

This expression, if evaluated, will get de-sugared into: `:(:call, :+, 1, 1)`

Which is a 4 element array with 2 symbols and 2 integers.

#### Example 1: Hello world macro

*Definition:*

This is [an example from the main docs](http://docs.julialang.org/en/release-0.3/manual/metaprogramming/) of how to define a macro.


```julia
macro sayhello()
  return :( println("Hello, world"))
end
```

*Usage:*

Macros in Julia are used with the `@` sign

```julia
@sayhello # "Hello, world"
```

#### Example 2: Assert macro

*Definition:*

```julia
macro assert(ex)
    return :($ex ? nothing : error("Assertion failed: ", $(string(ex))))
end
```

*Usage:*
```julia
@assert 1==0
# ERROR: assertion failed: 1 == 0
# in error at error.jl:21
```


## Perl 6

Macros in Perl 6, like C, are just glorified string substitution.

```perl
macro add($x,$y) { "($x + $y)" }
```


## Python

Examples are taken from [macropy](https://github.com/lihaoyi/macropy).

Note: Python frowns against the use of macros and most people are unaware of their existence.


#### Example 1:

Auto create a simple class that functions as a struct without having to manually override all the necessary methods.

```python
@case
class Point(x, y): pass

p = Point(1, 2)

print str(p) # Point(1, 2)
print p.x    # 1
print p.y    # 2
print Point(1, 2) == Point(1, 2) # True
x, y = p
print x, y   # 1 2

```

#### Example 2:

A macro to turn classes into enums.

```python
@enum
class Direction:
    North, South, East, West

print Direction(name="North") # Direction.North

print Direction.South.name    # South

print Direction(id=2)         # Direction.East

print Direction.West.id       # 3

print Direction.North.next    # Direction.South
print Direction.West.prev     # Direction.East

print Direction.all
# [Direction.North, Direction.East, Direction.South, Direction.West]

```


## Rust

Rust has a hygienic macro system which is basically a function from Expression to Expression. In essence, like in lispy-languages, it just takes in syntax and output different syntax.

As in Python, macros in Rust are not recommended although Rust does not take such a harsh stance against macros. The refer to macros in their documentation as a "feature of last resort".


#### Example: Vector macro

This example is taken from the [Rust documentation on macros](https://doc.rust-lang.org/book/macros.html).


*Definition:*

```rust
macro_rules! vec {
    ( $( $x:expr ),* ) => {
        {
            let mut temp_vec = Vec::new();
            $(
                temp_vec.push($x);
            )*
            temp_vec
        }
    };
}
```

*Usage:*

```rust
let x: Vec<u32> = vec![1, 2, 3];
```


*Note: Rust uses `!` to emphasize that the identifier is a macro as opposed to a function. This is intended to warn the user that they are about to use a macro (which could potentially be dangerous) as opposed to a regular function which has more guarantees.*
