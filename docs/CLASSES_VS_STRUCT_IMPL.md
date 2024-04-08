# Getting Started with Rust

## C++/Python Classes vs. Rust Struct and Impl 

In Rust, `struct` and `impl` are used to create custom data types and methods, similar to classes in Python and C++.

A `struct` in Rust is a custom data type that lets you name and package together multiple related values. It's similar to a class in Python or C++, but without methods.

```rust
struct Point {
    x: f32,
    y: f32,
}
```

The `impl` keyword in Rust is used to define methods on structs. This is similar to defining methods within a class in Python or C++. However, in Rust, the methods are separated from the data definition.

```rust
impl Point {
    fn distance_from_origin(&self) -> f32 {
        (self.x.powi(2) + self.y.powi(2)).sqrt()
    }
}
```

In Python or C++, methods are defined within the class itself:

```python
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def distance_from_origin(self):
        return (self.x**2 + self.y**2)**0.5
```

```cpp
class Point {
public:
    Point(float x, float y) : x(x), y(y) {}

    float distance_from_origin() {
        return sqrt(x*x + y*y);
    }

private:
    float x, y;
};
```

One key difference is that Rust enforces a strict separation between data and behavior, while Python and C++ allow them to be bundled together in a class. This reflects Rust's focus on systems programming and performance, where such a separation can provide more control.

## Constructor Equivalent

In Rust, constructors are not built into the language's structure syntax like they are in some other languages. Instead, you typically create a constructor by defining a function that returns an instance of the struct. By convention, this function is often named `new`.

Here's how you could implement a constructor for the `Point` struct:

```rust
impl Point {
    fn new(x: f32, y: f32) -> Self {
        Self { x, y }
    }
}
```

In this code, `impl Point` begins an implementation block for the `Point` struct. Inside this block, a function `new` is defined. This function takes two parameters, `x` and `y`, and returns an instance of `Point` where the `x` and `y` fields are initialized with the values of the `x` and `y` parameters, respectively.

The `Self` keyword is an alias for the type that this function is implemented on, in this case, `Point`. So `Self { x, y }` is equivalent to `Point { x, y }`.

You can now create a new `Point` instance like this:

```rust
let p = Point::new(1.0, 2.0);
```

## Clone Trait

The `#[derive(Clone)]` line in your Rust code is a derive attribute. This attribute automatically implements the `Clone` trait for the type it's applied to.

In Rust, traits are a way to define shared behavior across types. The `Clone` trait, in particular, provides functionality for creating a duplicate of an object.

When you add `#[derive(Clone)]` above a struct or enum definition, you're telling the Rust compiler to automatically generate the necessary code to make instances of that type cloneable.

Here's an example:

```rust
#[derive(Clone)]
struct Point {
    x: f32,
    y: f32,
}
```

With this code, you can now create a clone of a `Point` instance:

```rust
let p1 = Point { x: 1.0, y: 2.0 };
let p2 = p1.clone(); // p2 is a separate copy of p1
```

Without the `#[derive(Clone)]` attribute, trying to call `.clone()` on a `Point` instance would result in a compile error, because the `Point` type does not implement the `Clone` trait.