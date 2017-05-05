## Automatic Verification of JVM Bytecode Programs

```
$ git clone https://github.com/gt-wunderhorn/gt-wunderhorn
$ cd gt-wunderhorn
$ sudo make configure
$ make
$ sudo make install
$ cd example
$ wunderhorn Fib.java
Safe!
```

[Wunderhorn](https://github.com/gt-wunderhorn/gt-wunderhorn)
is a tool, built in [OCaml](http://ocaml.org/),
for the automatic static analysis of Java Programs.
Wunderhorn is an integration of powerful existing technologies:

  * [Sawja](http://sawja.inria.fr/) is used to reduce JVM bytecode programs to
    the a stackless, unstructured intermediate representation.
  * We apply the semantics of the instructions in the intermediate representation
    to construct a system of Constrained Horn Clauses (CHC System).
  * [Z3](https://github.com/Z3Prover/z3) can solve CHC Systems using Fixed-Point
    solvers. We use the [Duality](https://www.microsoft.com/en-us/research/project/duality/)
    engine to solve CHC systems.

### Custom static assertions

Wunderhorn allows users to specify custom properties for their programs. In this
example, the user wants to make sure that their naive implementation of her Fibonnaci
function implementation always returns a non-negative value.

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

```
$ wunderhorn Fib.java
Safe!
```

### Automatic Error Detection

Wunderhorn also performs *automatic* error detection. In this example, the user has
written an implementation of a function for finding the *least common multiple* of
two integers. However, this particular implementation is partial. Computing the
LCM of 0 and 0 results in a division by 0. Wunderhorn finds this particular bug
and alerts the user so she can fix it.

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

```
$ wunderhorn LCM.java
Division by 0 possible at LCM.lcm line 13.
```

Wunderhorn automatically checks for division by 0 and array out of bounds accesses.

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
          <figure>
            <img src="mug-shot/eden.jpg" width="150px"/>
            <figcaption>Michael Eden</figcaption>
          </figure>
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

## References

[Microsoft Z3 Theorem Prover](https://github.com/Z3Prover/z3)

[Sawja Static Analysis Workshop for Java](http://sawja.inria.fr/)

[Duality](https://www.microsoft.com/en-us/research/project/duality/)
