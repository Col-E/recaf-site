# CallGraph

The `InheritanceGraph` allows you to navigate between methods, linked by calls to one another.

## Graph readiness

The call graph is populated in the background when a workspace is loaded and is not immediately ready for use. Before you attempt to use graph operations check the value of the graph's `ObservableBoolean isReady()` method. You can register a listener on the `ObservableBoolean` to operate immediately once it is ready.

```java
ObservableBoolean ready = graph.isReady();

// Use a listener to wait until the graph is ready for use
ready.addChangeListener((ob, old, current) -> {
	if (current) {
		// do things
	}
});

// Or use a sleep loop, or any other blocking mechanism
while (!ready.getValue()) {
	Thread.sleep(100);
}
```

## Getting an entry point

The graph is has its vertices bundled by which class defines each method. So to get your entry-point vertex in the graph you need the `JvmClassInfo` reference of the class defining the method you want to look at.

```java
// Given this example
class Foo {
    public static void main(String[] args) { System.out.println("hello"); }
}

// Get the class reference
ClassPathNode clsPath = workspace.findJvmClass("com/example/Foo");
if (clsPath == null) return;
JvmClassInfo cls = clsPath.getValue().asJvmClass();

// Get the methods container for the class
ClassMethodsContainer containerMain = graph.getClassMethodsContainer(cls);

// Get the method in the container by name/descriptor
MethodVertex mainVertex = containerMain.getVertex("main", "([Ljava/lang/String;)V");
```

## Navigating the graph

The `MethodVertex` has methods for:

- Giving current information about the method definition
  -  `MethodRef getMethod()` - Holds `String` values outlining the declared method and its defining class. Always present.
  - `MethodMember getResolvedMethod()` - Holds workspace references to the declared method, may be `null` if the declaring class type and/or method couldn't be found in the workspace.
- Getting methods that this definition calls to
  - `Collection<MethodVertex> getCalls()`
- Getting methods that call this definition
  - `Collection<MethodVertex> getCallers()`

Following the example from before, we should see that the only call from `main` is `PrintStream.println(String)`.

```java
for (MethodVertex call : mainVertex.getCalls()) { // Only one value
    MethodRef ref = call.getMethod();
    String owner = ref.owner(); // Will be 'java/io/PrintStream'
    String name = ref.name(); // Will be 'println'
    String desc = ref.desc(); // Will be '(Ljava/lang/String;)V'
}
```

Similarly if you looked up the vertex for `PrintStream.println(String)` and checked `getCallers()` you would find `Foo.main(String[])`.