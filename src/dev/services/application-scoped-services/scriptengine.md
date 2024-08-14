# ScriptEngine

The script engine is a service used to:

* Run single Java files as [Recaf scripts](../../plugins-and-scripts/scripts.md).
* Compile single Java files, without running them, and yielding the `java.lang.Class` of the generated script.

## Running scripts

Calling `run(String)` will asynchronously compile and run the passed script contents. As documented in the scripting section, these can be as short or long as you desire. Here are some examples of varying complexity:

```java
@Inject
ScriptEngine engine;

// A simple one-liner
engine.run("System.setProperty(\"foo\", \"bar\");").thenAccept(result -> {
    if (result.wasSuccess())
        System.out.println(System.getPropert("foo")); // Will print "bar"
});

// Recaf's script system allows you to also define full classes. Any method 'void run()' will be executed.
// It also supports injection of *any* of Recaf's services of *any* scope. If a workspace is currently
// active you can inject it or any workspace-scoped service.
// Check the scripting section for more information.
String code = """
		public class Test implements Runnable {
			@Inject
			JavacCompiler compiler;
			
			@Override
			public void run() {
				System.out.println("hello: " + compiler);
				if (compiler == null) throw new IllegalStateException();
			}
		}
    	""";
engine.run(code).thenAccept(result -> {
    // At this point we printed 'hello: JavacCompiler@71841' or whatever the instance hash is at the moment.
});
```

## Compiling scripts

If you wish to get the `java.lang.Class` of the generated script without immediately running it, you can use `compile(String)` to asynchronously get the compiled class.

```java
engine.compile("System.setProperty(\"foo\", \"bar\");").thenAccept(result -> {
    if (result.wasSuccess()) {
        Class<?> scriptClass = result.cls();
        // Do what you want with the class
    }
});
```