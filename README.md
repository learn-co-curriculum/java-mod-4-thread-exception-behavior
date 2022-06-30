# Threads Exception Behavior

## Learning Goals

- Explain what effect exceptions have in threads.

## Introduction

When we run a single threaded program and it throws an exception which isn’t
caught, the program will terminate. But if we spawn a new thread and the
exception is thrown inside it, the thread will stop execution but the main
program will run to completion with an error code.

## Exception Behavior

Let’s look at what happens when the `main` thread throws an exception and it’s
not caught.

```java
class Example {
    public static void main(String[] args) throws InterruptedException {
        System.out.println("start");
        System.out.println(1 / 0);
        System.out.println("end");
    }
}
```

```plaintext
start
Exception in thread "main" java.lang.ArithmeticException: / by zero
	at Example.main(Example.java:37)

Process finished with exit code 1
```

As expected, the process doesn’t run to completion. The exit code `1` signifies
that the process completed with an error.

Let’s observe what happens when an exception thrown inside a thread.

```java
class Example {
    public static void main(String[] args) throws InterruptedException {
        Thread t = new Thread(() -> System.out.println(1 / 0));
        System.out.println("start");
        t.start();
        System.out.println("done");
    }
}
```

```plaintext
start
done
Exception in thread "Thread-0" java.lang.ArithmeticException: / by zero
	at Example.lambda$main$0(Example.java:36)
	at java.base/java.lang.Thread.run(Thread.java:833)

Process finished with exit code 0
```

We can see that the code in the `main` thread is fully executed even though the
thread throws an exception. The exit code `0` signifies that the process
completed successfully.

## Conclusion

We’ve learned that exceptions in threads have to be handled independently. A
process that is running non-daemon threads won’t be stopped even if those
threads throw uncaught exceptions. We should handle exceptions in threads as a
best practice.
