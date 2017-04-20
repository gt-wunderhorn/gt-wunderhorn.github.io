## Automatic Verification of JVM Bytecode Programs

### Custom static assertions

```Java
public class Main {
  public static int fib(int n) {
    if (n <= 0) return 0;
    if (n == 1) return 1;
    return fib(n-1) + fib(n-2);
  }

  public static void main(String[] args) {
    ensure(fib(4) == 3);
  }
}
```

```
$ SafetyProject MyMath.java
Safe!
```


### Automatic Error Detection

```Java
public class MyMath {
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
}
```

```
$ SafetyProject MyMath.java
Possible division by 0!
```

## References
[Microsoft Z3 Theorem Prover](https://github.com/Z3Prover/z3)

[Sawja Static Analysis Workshop for Java](http://sawja.inria.fr/)
