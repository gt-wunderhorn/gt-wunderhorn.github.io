# Automatic JVM Safety Verification Using wunderhorn

`wunderhorn` is a tool that takes a program `P` and a _safety
property_ `Q` and attempts to determine if `P` satisfies `Q`. A
program can be any program represented as Java source or a JVM
bytecode classfile. A safety property is an assertion. If each run of
a program `P` that reaches an assertion `Q` satisfies the condition of
`Q`, then `P` satisfies `Q`.

### Verifying freedom from language-level errors

`wunderhorn` automatically attempts to verify that a given program
cannot throw one of several exceptions that is typically a result of
an error, such as:

* Accessing an array out of bounds

* Attempting to divide by `0`

For example, consider the following implementation of a function that,
given integers `m` and `n`, computes the _Least Common Multiple_ (LCM)
of `m` and `n`:

```java
public class LCM {
  static int gcd(int a, int b) {
    int t;
    while (b != 0) {
      t = a % b;
      a = b;
      b = t;
    }
    return a;
  }

  static int lcm(int a, int b) {
    return a / gcd(a, b) * b;
  }

  public static void main(String[] args) {
    int x = Wunderhorn.arby_int();
    int y = Wunderhorn.arby_int();
    lcm(x, y);
  }
}
```

This implementation is not safe: if it is given input `m = n = 0`, it
will attempt to divide a number by `0`, and throw an exception.

When `wunderhorn` is given the above implementation, it reports that
that program is not safe:

```
$ wunderhorn LCM.java
Division by 0 possible at LCM.lcm line 13.
```

### Verifying satisfaction of custom static assertions

`wunderhorn` allows users to express application-specific properties
as assertions that that are verified statically. Consider the
following implementation of the Fibonacci function, annotated with an
assertion that specifies that the function `fib` should always return
a non-negative value:

```java
public class Fib {
  public static int fib(int n) {
    if (n <= 0) return 0;
    if (n == 1) return 1;
    return fib(n-1) + fib(n-2);
  }

  public static void main(String[] args) {
    int x = Wunderhorn.arby_int();
    Wunderhorn.ensure(fib(x) >= 0);
  }
}
```

`wunderhorn` reports that `Fib` satisfies its assertion:

```
$ wunderhorn Fib.java
Safe!
```

## Building wunderhorn

To build `wunderhorn`, download a copy and `make` it:
```
$ git clone https://github.com/gt-wunderhorn/gt-wunderhorn
$ cd gt-wunderhorn
$ sudo make configure
$ make
$ sudo make install
Safe!
```

## Using wunderhorn

To use `wunderhorn`, run it on a Java source or JVM bytecode class
that contains a `main` method, and is optionally annotated with
assertions. Here is a shell session for running `wunderhorn` on one of
its included examples, `Fib.java` given above:

```
$ cd example
$ wunderhorn Fib.java
Safe!
```

## Implementation

`wunderhorn` is implemented in OCaml [OCaml](http://ocaml.org/). It
uses several existing frameworks for performing program-analysis
tasks. In particular, it uses:

  * [Sawja](http://sawja.inria.fr/), which translats a given JVM
    bytecode class to a stackless low-level intermediate
    representation.

  * [Z3](https://github.com/Z3Prover/z3), which attempts to solve a
    given CHC system that formulates verifying program safety. In
    particular, it uses the
    [Duality](https://www.microsoft.com/en-us/research/project/duality/)
    solver implemented within Z3.

## Developers

<center>
  <table style="border-collapse: collapse; border: none;">
    <tr style="border-collapse: collapse; border: none;">
      <td style="border-collapse: collapse; border: none;">
        <a href="http://daheath.github.io">
          <figure>
            <img src="mug-shot/heath.jpg" width="150px">
            <figcaption>David Heath</figcaption>
          </figure>
        </a>
      </td>

      <td style="border-collapse: collapse; border: none;">
        <a href="http://icanteden.com">
          <figure>
            <img src="mug-shot/eden.jpg" width="150px"/>
            <figcaption>Michael Eden</figcaption>
          </figure>
        </a>
      </td>

      <td style="border-collapse: collapse; border: none;">
        <a href="http://www.cc.gatech.edu/~wharris">
          <figure>
            <img src="mug-shot/harris.jpg" width="150px">
            <figcaption>Bill Harris</figcaption>
          </figure>
        </a>
      </td>
    </tr>
  </table>
</center>

