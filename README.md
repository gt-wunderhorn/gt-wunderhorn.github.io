## Automatic Verification of JVM Bytecode Programs

Wunderhorn is a tool for the automatic static analysis of Java Programs.
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

```Java
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

```Java
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

Wunderhorn automatically checks for division by 0, and array out of bounds accesses.

## References
[Microsoft Z3 Theorem Prover](https://github.com/Z3Prover/z3)
[Sawja Static Analysis Workshop for Java](http://sawja.inria.fr/)
[Duality](https://www.microsoft.com/en-us/research/project/duality/)
