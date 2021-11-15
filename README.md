# Dangle: Java Coroutines
## Conversion from Sequential Style (SS) code to Continuation Passing Style (CPS) code

### Example

Original Code:
```java
@Suspend
public static String fetch(final String a) {
  final String a = abc("test");
  final String b = def(a);
  return a + b;
}

@Suspend
public static String abc(final String a) {
   return a + "123abc";
}

@Suspend
public static String def(final String a) {
   return a + "456def";
}
```

Transformed Code:
```java
@Suspend
public static void fetch(final String a, final TaskExecutor exe, final ContinuationFactory<String> continuation) {
    abc(a, exe, new Main$fetch$continuation$1(exe, continuation);
}

@Suspend
public static void abc(final String a, final TaskExecutor exe, final Continuation<String> continuation) {
   exe.submit(continuation, a + "123abc");
}

@Suspend
public static void def(final String a, final TaskExecutor exe, final Continuation<String> continuation) {
   exe.submit(continuation, "456def");
}


class Main$fetch$continuation$1 implements Continuation<String> {
    private final TaskExecutor exe;
    private final Continuation<String> resultContinuation;
    
    public void execute(final String a, final StateContainer container) {
         def(a, exe, new Main$fetch$continuation$2(exe, resultContinuation, a));
    }
}

class Main$fetch$continuation$2 implements Continuation<String> {
    private final TaskExecutor exe;
    private final Continuation<String> resultContinuation;
    private final String a;
    public void execute(final String b) {
         exe.submit(resultContinuation, a + b);
    }
}
```
