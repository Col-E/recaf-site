# JVM Execution: Stack + Locals

The Java Virtual Machine _(JVM)_ executes method bytecode using a stack-based architecture. Each thread in a Java program has its own private [JVM stack](https://docs.oracle.com/javase/specs/jvms/se25/html/jvms-2.html#jvms-2.5.2), which consists of stack frames. A new stack frame is created and pushed onto the stack every time a method is invoked. When the method completes _(normally or via a thrown exception)_, its frame is popped and discarded. Each stack frame represents the state of a single method invocation and contains:

- **Local Variables**: An array of variables holding primitive and reference types. Contents are stored and accessed in the array via the variable's index _(Recaf's assembler will use the name of variables rather than its index)_.
- **Operand Stack**: A Last-In-First-Out _(LIFO)_ structure used as temporary space for intermediate values during computations.

The JVM also maintains a program counter _(PC)_ per thread, which points to the current bytecode instruction being executed in the method. The PC advances sequentially as instructions are processed, unless modified by control flow instructions.

## Execution Flow Overview

1. When a method is invoked:
   - A new frame is created for the invoked method.
   - The caller pushes copies of the argument values _(primitive values or copies of object references)_ onto the invoked method's operand stack.  
   - Execution in the caller is paused at the instruction immediately after the method invocation, ready to resume once the invoked method completes its own execution and _(if applicable)_ pushes a return value onto the caller's operand stack.
2. Instructions execute one by one:
   - Load constants or variables → push to the operand stack.
   - Store result → pop from the stack to a variable.
   - Arithmetic/logic → pop operands, compute, push result.
   - Control flow → conditionally jump to a label.
   - Invoke methods → see above description of method invocation.
3. On return:
   - The frame is popped, and the value on top of the stack _(if any is present)_ is pushed to the caller's operand stack.

## Examples

### Local variables and arithmetic

```java
// int two = 2;
iconst_2 
istore two

// int ten = 10;
bipush 10
istore ten

// int result = 0;
iconst_0 
istore result

// result = ten * ten;
iload ten
dup
imul 
istore result

// result -= 98;
iinc result -98

// result -= (int) Math.pow(result, 6);
iload result
iload result
i2d 
ldc 6D
invokestatic java/lang/Math.pow (DD)D
d2i 
isub 
istore result

// return result;
iload result
ireturn 
```

### Control flow handling of a `for` loop

```java
// int sum = 0;
iconst_0 
istore sum

// int i = 0;
iconst_0 
istore i

// Start of for loop
FOR:
    // if (i >= 0) break;
    iload i
    bipush 100
    if_icmpge EXIT

	// sum += i;
    iload sum
    iload i
    iadd 
    istore sum

	// i++;
    iinc i 1
    goto FOR
// End of for loop
EXIT: 

// return sum;
iload sum
ireturn
```

