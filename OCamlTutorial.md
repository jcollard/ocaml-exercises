## Introduction

The following Exercises were taken from Arjun Guha's Fall 2013 Programming Languages Course. The original document might still be available here (http://people.cs.umass.edu/~arjun/courses/cs691f/assignments/ocaml-tutorial.html).

## Basic Programming
A simple OCaml program is a sequence of _declarations_, followed by a _main expression_. You must use a double-semicolon (`;;`) 
to separate the declarations from the main expression. E.g.:

```ocaml

    let x : int = 23
    let y : int = 60
    let sum : int = x + y
    let message : string = "The sum is " ^ string_of_int sum ^ ".\n"   

    ;;

    print_string message 

```

In this example, all the declarations are _let declarations_. (We introduce a few other kinds of declarations momentarily.) 
A let declaration states the name of the bound identifier, its type, and the bound expression. Unlike more complex languages, 
OCaml does not automatically convert between values of different types. For example, the code above explicitly applies 
[string_of_int](http://caml.inria.fr/pub/docs/manual-ocaml/libref/Pervasives.html#VALstring_of_int) . 
Without this conversion, the OCaml compiler woudl fail with a type error:

`This expression has type int but an expression was expected of type string.`

Similarly, OCaml has no function or operator overloading. In many languages, `x + y` may mean "add two numbers" 
or "concatenate two strings". In OCaml these two operations are distinct: `x ^ y` means string concatenation and `x + y`
means integer addition. Incidentally, `x +. y` means floating point addition.

In OCaml, we can declare functions using a variation of let declarations:

```ocaml

    let increment (a : int) : int =
      a + 1

    let add_three (x : int) (y : int) (z : int) : int =
      x + y + z

    let make_message (n : int) : string =
      "The sum is " ^ string_of_int n ^ ".\n"   

    ;;

    print_string (make_message (add_three 1 2 3))

```

In these declarations, each function argument has a name and type enclosed in parentheses. 
The final type in the declaration is the type of the function result.

Pay close attention to the syntax of function application in the main expression. 
A function application uses spaces to separate the function from each argument. 
The parentheses simply ensure that arguments are properly grouped. For example, 
suppose we missed the inner parentheses and wrote:

```ocaml

    print_string (make_message add_three 1 2 3)

```

This expression applies `make_message` to four arguments, but it expects only one, so we have a type error. 
Similarly, suppose we missed the outer parentheses:

```ocaml

    print_string make_message (add_three 1 2 3)

```

This expression applies `print_string` to two arguments, but it expects only one, so we have another type error. 
When it doubt, use parentheses for clarity.

In OCaml, `let` does not introduce a recursive binding. Therefore, to declare a recursive function, we must use`let rec` instead:

```ocaml

    let rec factorial (n : int) : int =
      if n = 0 then
        1
      else
        n * factorial (n - 1)

    ;;

    print_string ("factorial 5 = " ^ string_of_int (factorial 5) ^ "\n")

```

### Exercise 2

Write a function, `seconds_since_midnight h m s`, which returns the number of seconds elapsed since midnight. 
Your function should have the following type.

```ocaml

    val seconds_since_midnight : int -> int -> int -> int

```

### Exercise 3

Write a function, `fibonacci n`, which compute the _n_th Fibonacci number. The function should have the following type:

```ocaml

    val fibonacci : int -> int

```

* * *

## Declaring New Types

The previous section covered let declarations. This section introduces _type declarations_. Here is an example:

```ocaml

    type point =
        Point2D of int * int

```

This type declaration introduces a type called `point`. It has one _constructor_ called `Point2D` that takes two arguments,
both integers. Type names must begin with a lowercase letter and constructor names must begin with an uppercase letter.

Given this declaration, we can use the constructor to create new points:

```ocaml

    let origin : point = Point2D (0, 0)

    let pt1 : point = Point2D (10, 20)

    let pt2 : point = Point2D (-40, 50)

```

Above, the parentheses and commas are required: the constructor name must be followed by a comma-separated list of arguments 
enclosed in parentheses.

### Exercise 4

Define a type, `time`, which holds the hour, minute, and second as separate values.

* * *

## Pattern Matching

Given a point value, we can use _pattern matching_ to extract its components, as these functions do:

```ocaml
    let double_point (p : point) : point =
      match p with
        Point2D (x, y) -> Point2D (2 * x, 2 * y)

    let add_point (p1 : point) (p2 : point) : point =
      match (p1, p2) with
        (Point2D (x1, y1), Point2D (x2, y2)) -> Point2D (x1 + x2, y1 + y2)

    let string_of_point (p : point) : string =
      match p with
        Point2D (x, y) -> "<" ^ string_of_int x ^ ", " ^ string_of_int y ^ ">"

```

In the following several exercises, you will use pattern matching to write functions that manipulate points. 
The OCaml manual has a section on 
[integer arithmetic](http://caml.inria.fr/pub/docs/manual-ocaml/libref/Pervasives.html#6_Integerarithmetic) that will be 
helpful.

### Exercise 5

Write a function `seconds_since_midnight2` with the following type:

```ocaml

    val seconds_since_midnight2 : time -> int

```

### Exercise 6

Write a function `seconds_to_time t`, which takes the seconds elapsed since midnight as its argument and 
returns the coresponding time.

```ocaml

    val seconds_to_time : int -> time

```

### Exercise 7

Write a function `time_diff t1 t2`, which calculates the number of seconds that have elapsed between `t1` and `t2`:

```ocaml

    val time_diff : time -> time -> int

```

### Exercise 8

Write a function `tick t`, which increments `t` by one second and returns the new time:

```ocaml

    val tick : time -> time

```

* * *

## Recursive Data Structures: Lists

For this part of the tutorial, we will work with a type declaration for lists of integers. There are two kinds of lists: 
the empty list and lists that have a single integer and a a reference to the rest of the list. We can specify the shape of 
lists using a type declaration with two constructors:

```ocaml

    type intlist =
      | Cons of int * intlist
      | Empty

```

For example, here is a list of numbers from -1 through 4:

```ocaml

    let from_minus_1_to_4 =
      Cons (-1, Cons (0, Cons (1, Cons (2, Cons (3, (Cons (4, Empty)))))))

```

We can declare list processing functions using the same constructs we used to write date processing functions. However, 
since lists are recursively defined, most interesting list processing functions need to be recursive, too. For example, here is a 
function that calcululates the length of a list:

### Exercise 9

Write a function `all_positive lst`, which returns `true` if all the integers in `lst` are positive.

```ocaml

    val all_positive : intlist -> bool

```

### Exercise 10

Write a function `all_even lst`, which returns `true` if all the integers in `lst` are even numbers.

```ocaml

    val all_even : intlist -> bool

```

### Exercise 11

Write the function `is_sorted lst` to determine if the integers in `lst` are in sorted (ascending).

```ocaml

    val is_sorted : intlist -> bool

```

_Hint:_ You will need to write a recursive helper function.


### Exercise 12

Write the function `insert_sorted n lst`, which inserts `n` into the sorted list `lst` and preserves the sort-order.

### Exercise 13

Write the `insertion_sort` function, using `insert_sorted` as a helper.

```ocaml

    val insertion_sort : intlist -> intlist

```

* * *

## Evaluating Arithmetic Expressions

For this part of the tutorial, you will write an evaluator and pretty-printer for simple arithmetic expressions—the simplest of 
interpreters. Notably, this interpreter does not use any OCaml concept beyond those introduced above.

For the exercises below, use the following type declaration that represents arithmetic expressions.

```ocaml

    type exp =
      | Int of int
      | Add of exp * exp
      | Mul of exp * exp
      

```

### Exercise 14

Encode the following arithmetic expressions as `exp`s:

1.  10 + 5
2.  (2 + 3) * 5
3.  3 * 0 * 3 * 5

### Exercise 15

Write the function `eval e`, which reduces expressions to integer values:

```ocaml

    val eval : exp -> int

```

### Exercise 16

Write the function `print e`, which returns a string representing `e`:

```ocaml

    val print : exp -> string

```

The string should print arithmetic operators using infix notation and properly parenthesize expressions. For example:

```ocaml
print (Add (Int 10, Int 5)) = "(10 + 5)"
print (Mul (Add (Int 2, Int 3), Int 5)) = "((2 + 3) * 5)"
print (Mul ((Mul (Int 3, Int 0)), Mul (Int 3, Int 5))) = "((3 * 0) * (3 * 5))"
```

Use the [^ operator](http://caml.inria.fr/pub/docs/manual-ocaml/libref/Pervasives.html#VAL(^)) to concatenate strings and 
the [string_of_int](http://caml.inria.fr/pub/docs/manual-ocaml/libref/Pervasives.html#VALstring_of_int) function.

### Exercise 17

The `print` function that you wrote above may be naive about how it prints parentheses. For example, `1 + 2 * 3` is usually
interpreted as `1 + (2 * 3)` because the `*` operator <emph>binds tighter</emph> than the `+` operator. Write the `pretty_print e` 
function, which prints parentheses only when necessary:

```ocaml

    val pretty_print : exp -> string

```

_Hint_: You will need a helper function, `pp cxt e`, where `e` is the expression to print and `cxt` identifies the shape of the 
immediately surrounding context.
