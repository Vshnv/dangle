# Dangle: Java Coroutines
## Conversion from Sequential Style (SS) code to Continuation Passing Style (CPS) code

### Rules:

1. All instructions after a Suspend function call inside Suspend function calls are moved to a new class (which implements continuation).
2. When instrutructions are moved to the continuation class, the class should accept parameters of variables from caller for required variables for the rest of the continuation.
3. All return instructions would be switched with invoking `TaskExecutor#execute`.


### Example:

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
public static void fetch(final String a, final TaskExecutor exe, final Continuation<String> continuation) {
    abc(a, exe, new Main$fetch$continuation$1(exe, continuation);
}

@Suspend
public static void abc(final String a, final TaskExecutor exe, final Continuation<String> continuation) {
   exe.submit(continuation, a + "123abc");
}

@Suspend
public static void def(final String a, final TaskExecutor exe, final Continuation<String> continuation) {
   exe.submit(continuation, a + "456def");
}

private static class Main$fetch$continuation$1 implements Continuation<String> {
    private final TaskExecutor exe;
    private final Continuation<String> resultContinuation;
    
    public Main$fetch$continuation$1(final TaskExecutor exe, final COntinuation<String> res) {
        this.exe = exe;
        this.resultContinuation = res;
    }
    
    public void execute(final String a, final StateContainer container) {
        def(a, exe, new Main$fetch$continuation$2(exe, resultContinuation, a));
    }
}

private static class Main$fetch$continuation$2 implements Continuation<String> {
    private final TaskExecutor exe;
    private final Continuation<String> resultContinuation;
    private final String a;
    
    public Main$fetch$continuation$1(final TaskExecutor exe, final COntinuation<String> res, final String a) {
        this.exe = exe;
        this.resultContinuation = res;
        this.a = a;
    }
    
    public void execute(final String b) {
        exe.submit(resultContinuation, a + b);
    }
}
```
