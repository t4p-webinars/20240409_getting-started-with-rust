# Getting Started with Rust

## Ownership

Ownership is Rust's most unique feature, and it enables Rust to make memory safety guarantees without needing a garbage collector.

```rust
struct Point {
    x: f64,
    y: f64,
}

fn print_point(p: Point) {
    println!("point coordinates: ({}, {})", p.x, p.y);
}

fn main() {
    let p = Point { x: 2.0, y: 3.5 };
    print_point(p);

    // function fails because the first function call transfer ownership of p to print_point
    // print_point(p);
}
```

To fix the code, we can borrow the `Point` struct in the `print_point` function.


```rust
struct Point {
    x: f64,
    y: f64,
}

fn print_point(p: &Point) {
    println!("point coordinates: ({}, {})", p.x, p.y);
}

fn main() {
    let p = Point { x: 2.0, y: 3.5 };
    print_point(&p);

    // function works because the first function call borrows p
    // print_point(&p);
}
```

## Mutability

By default, variables are immutable in Rust. To make a variable mutable, you need to use the `mut` keyword.

```rust
struct Point {
    x: f64,
    y: f64,
}

fn main() {
    let p = Point { x: 2.0, y: 3.5 };
    
    // error[E0596]: cannot borrow immutable local variable `p` as mutable
    // p.x = 3.0;
}
```

Fix the code by making the `Point` struct mutable.

```rust
struct Point {
    x: f64,
    y: f64,
}

fn main() {
    let mut p = Point { x: 2.0, y: 3.5 };
    p.x = 3.0;
}
```

## Mutability and Borrowing

You can't borrow a mutable variable more than once in the same scope.

```rust
fn main() {
    let mut x = 5;
    let y = &mut x;
    // error[E0499]: cannot borrow `x` as mutable more than once at a time
    let z = &mut x;

    println!("{}, {}", y, z);
}
```

To fix the code, you can use a different scope for each borrow.

```rust
fn main() {
    let mut x = 5;
    {
        let y = &mut x;
        println!("{}", y);
    }
    let z = &mut x;
    println!("{}", z);
}
```

The `update_point` function below will fail to compile because the `Point` struct is immutable.

```rust
struct Point {
    x: f64,
    y: f64,
}

fn update_point(p: &Point) {
    p.x = 3.0;
    p.y = 4.0;
}

fn main() {
    let p = Point { x: 2.0, y: 3.5 };
    update_point(&p);
    println!("x: {}, y: {}", p.x, p.y);
}
```

To fix the code, you can make the `Point` struct mutable and update the `update_point` function to take a mutable reference to `Point`.

```rust
struct Point {
    x: f64,
    y: f64,
}

fn update_point(p: &mut Point) {
    p.x = 3.0;
    p.y = 4.0;
}

fn main() {
    let mut p = Point { x: 2.0, y: 3.5 };
    update_point(&mut p);
    println!("x: {}, y: {}", p.x, p.y);
}
```
