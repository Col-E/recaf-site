# Reference Obfuscation

In Java there are several instructions related to reading & writing to fields, and several more for invoking methods. These instructions refer to symbols in the constant pool including the name of the class defining the field or method, then the name and type of the field or method. Generally we'll call these *"references"*.  A majority of a Java application's logic consists of references *(as opposed to other things like math operations on primitives)* and thus a majority of the information about what an application does can be understood by analyzing these references.

Naturally, obfuscation aims to prevent reverse engineers from understanding an application, so how can these references be obfuscated? Simple, by swapping out the references with ones containing less valuable information. One way to do this is by abusing the `invokedynamic` instruction. What does this *"abuse"* look like though? Lets take a look at a normal use-case first.

```java
// Overcomplicated way to print "hi"
public class Demo {
	public static void main(String[] args) {
		exec(Demo::example);
	}

	// the code we want to run
	static void example() {
		System.out.println("hi");
	}

	// runs a runnable
	static void exec(Runnable r) {
		r.run();
	}
}
```

Lets disassemble this class and see what it looks like:

```java
.inner public static final {
    name: Lookup,
    inner: java/lang/invoke/MethodHandles$Lookup,
    outer: java/lang/invoke/MethodHandles
}
.super java/lang/Object
.class public Demo {
    .method public static main ([Ljava/lang/String;)V {
        code: {
        A:
            // Creates a "Runnable" via "LambdaMetafactory" with a MethodHandle pointing to 'void example()'
            //  First '()V' before the handle is the 'samType' (SAM is an acronym for single-abstract-method)
            //  MethodHandle = { invokestatic, Demo.example, ()V }
            //   - Outlines the handle dispatch type (mirroring the invoke instruction opcodes) 
            //   - Outlines where the method is defined (This Demo class)
            //   - Outlines the method signature (the name and type, example and ()V)
            //  Second '()V' after the handle is the 'instantiatedMethodType' must match 'samType' or have arguments with subtypes
            //   - Example: If you had 'samType = (Ljava/util/Collection;)V' you could use 'instantiatedMethodType = (Ljava/util/List;)V'
            invokedynamic run ()Ljava/lang/Runnable; LambdaMetafactory.metafactory { ()V, { invokestatic, Demo.example, ()V }, ()V }
            
            // Passes the "Runnable" as a normal parameter
            invokestatic Demo.exec (Ljava/lang/Runnable;)V
            return
        B:
        }
    }

    // The code we want to run
    .method static example ()V {
        code: {
        A:
            getstatic java/lang/System.out Ljava/io/PrintStream;
            ldc "hi"
            invokevirtual java/io/PrintStream.println (Ljava/lang/String;)V
            return
        B:
        }
    }

    .method static exec (Ljava/lang/Runnable;)V {
        parameters: { r },
        code: {
        A:
            // Just run the passed runnable
            aload r
            invokeinterface java/lang/Runnable.run ()V
            return
        B:
        }
    }
}
```

As you can see in the `main` method, the `invokedynamic` instruction can be used to create a new `Runnable` that is implemented by calling `example()`. In the source form this comes as a static method reference `Demo::example`. 

> But what if we used a lambda instead of a method reference?

When we write `exec(() -> example());` the idea still remains the same, but the compiler is now no longer aware that we are just calling `example()` and thus makes a new generated method to contain the lambda body. 

```java
.method public static main ([Ljava/lang/String;)V {
    code: {
    A:
        // Creates a "Runnable" via "metafactory" with a methodhandle pointing to compiler-generated method housing the contents of our lambda body
        //  MethodHandle = { invokestatic, Demo.lambda$main$0, ()V }
        //  - lambda$main$0 is the name of the compiler-generated method that holds our lambda body instructions
        invokedynamic run ()Ljava/lang/Runnable; LambdaMetafactory.metafactory { ()V, { invokestatic, Demo.lambda$main$0, ()V }, ()V }
        
        // Passes the "Runnable" as a normal parameter
        invokestatic Demo.exec (Ljava/lang/Runnable;)V
        return
    B:
    }
}

// The compiler auto-generates methods for each lambda you define in a source file.
// These generated methods are marked as 'synthetic' to denote the compiler made these.
.method private static synthetic lambda$main$0 ()V {
    code: {
    A:
        invokestatic software/Demo.example ()V
        return 
    B:
    }
}
```

You may be wondering what is this `LambdaMetafactory`? You can read [the full JavaDocs on it here](https://docs.oracle.com/javase/8/docs/api/java/lang/invoke/LambdaMetafactory.html) but the gist is that you give it a `MethodHandle` and a descriptor like `()V` and it will implement it the desired functional type provided as a return value such as `java/lang/Runnable`. Different methods in the class serve different purposes, but each that yields a `Callsite` is a method usable in the `invokedynamic` instruction. These methods are called *"bootstrap methods"*. This is a core offering of the Java language, and thus most decompilers are designed to understand this class's purpose. Thus, if you used a modern decompiler with this class it would spit out the `Demo::example` or `() -> example()` based on whichever one was used.

> Ok but how does an obfuscator make use of any of this if decompilers are so smart?

Compiling a lambda in Java source code will generate an `invokedynamic` using a bootstrap method specified in `LambdaMetafactory` but there is nothing stopping an obfuscator from creating its own bootstrap method. In fact, an obfuscator doesn't even need to pass a `MethodHandle` if it controls the the bootstrap method implementation. As an example, consider we make this change to the `main` method:

```java
invokedynamic run ()Ljava/lang/Runnable; MyBootstrap.find { "lookup-key" }
```

We can implement `MyObfuscator.find` like this:

```java
public class MyBootstrap {
    public static ConstantCallSite find(
            // These first 3 parameters are passed by the JVM and must always exist in a boostrap method definition.
            MethodHandles.Lookup lookup,
            String callerName,
            MethodType callerType,
            // Key value specified as the invokedynamic argument, ie "lookup-key" from the example above.
            String key) {
        ClassLoader loader = MyBootstrap.class.getClassLoader();

        MethodHandle handle = switch (key) {
            // Provide a case for each possible key we want to pass.
            // Our example only has the one..
            case "lookup-key" -> {
                // Create a MethodHandle by using the passed lookup then find our "Demo.example ()V"
                MethodType methodType = MethodType.fromMethodDescriptorString("()V", loader);
                yield lookup.findStatic(Demo.class, "example", methodType);
            }
            default -> null;
        };

        // Wrap the handle in a CallSite and we are done!
        return new ConstantCallSite(handle.asType(callerType));
    }
}
```

We are no longer using `LambdaMetafactory` and use a custom implementation that maps a passed `String` to a `MethodHandle`. There is no easy way to represent this new scheme as it no longer maps to a source code construct, making this class impossible to decompile back to its original form. Some will try their best to show the intention of the code, but are at the end of the day still wrong. Some examples:

- CFR: `MyBootstrap.find("lookup-key", this);`
- Procyon: `ProcyonInvokeDynamicHelper_1.invoke(this);`
- FernFlower: `MyBootstrap.find<"lookup-key">(this);`

Another fun fact is that despite the implication of the name `MethodType` you can also apply this form of reference obfuscation to field accessors. The `MethodHandle.Lookup` has methods for field reading and writing. You can quite easily create an obfuscator that replaces all of these references with a custom lookup. There's not too much of a concern about performance if you do this since these `ConstantCallSite` values are resolved once, and then cached.

## Can Recaf automatically restore the original form?

It depends, but for now the short answer is going to be *"not out of the box"*. The problem is that there are infinite ways to create a bootstrap method, so it becomes very hard to analyze obfuscated applications in a generic way that lets us determine the original intention. We could create specific transformers that target individual patterns used by specific obfuscators, but we are then in a game of cat-and-mouse. One small change in a new version of that specific obfuscator would break our transformer, and then we also need to support multiple versions of that transformer. This would be a massive time sink for us and also create lots of not so great duplicate code. For this reason, creating transformers for specific obfuscators is a process left to the you and the rest of the Recaf community *(Plugins can register their own transformers via [`TransformationManager`](..\..\dev\services\transformationmanager.md))*. Recaf will provide a host of generic obfuscation transformers and additional services for you to build off of though when creating your own transformer.