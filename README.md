# Dangle: Java Coroutines
## Conversion from Sequential Style (SS) code to Continuation Passing Style (CPS) code

### Example

Original Code:
```java
@Suspend
public static void String fetch(final String a) {
  final String a = abc("test");
  final String b = def(a);
  return a + b;
}

@Suspend
public static void String abc(final String a) {
   return a + "123abc);
}

@Suspend
public static void String def(final String a) {
   return a + "123abc);
}
```

Transformed Code:
```java
@Suspend
public static void fetch(final String a, final TaskExecutor exe, final Continuation continuation) {
  final String a = abc("test", exe, var1 -> {
    final String b = def(var1.get("v1"), exe, var2 -> {
        final String a_s = var2.get("v1");
        final String b_S = var2.get("v2");
        exe.submit(contuation.arg("v3",
    });
  });
}

@Suspend
public static void abc(final String a, final TaskExecutor exe, final Continuation continuation) {
   exe.submit(continuation, 
   continuation.execute(a + "123abc");
}

@Suspend
public static void def(final String a, final TaskExecutor exe, final Continuation continuation) {
   continuation.execute(a + "456def");
}
```
