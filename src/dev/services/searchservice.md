# SearchService

The search service allows you to search workspaces:

* For strings, numbers, and references to classes and/or members
* With the ability to cancel the search early
* With the ability to control which classes and files are searched in
* With the ability to control what results are included in the final output

## Query model

All searches are built from `Query` instances. There are three types of queries:

- `AndroidClassQuery` *(not yet implemented)*
- `JvmClassQuery`
- `FileQuery`

Each implementation creates a `SearchVisitor` that handles searching of individual items in the `Workspace`. Things like string, number, and reference searches all implement each of these query types that they are relevant for. For example the reference search only implements `AndroidClassQuery` and `JvmClassQuery` but string search implements all three since strings can appear in any of these places *(classes and files)*.

Searching for common cases like strings, numbers, and references are already implemented as queries and take in predicates for matching content. The following examples assume the following services are injected:

```java
@Inject
NumberPredicateProvider numMatchProvider;
@Inject
StringPredicateProvider strMatchProvider;
@Inject
SearchService searchService;
```

### String querying

```java
Results results = searchService.search(classesWorkspace, new StringQuery(strMatchProvider.newEqualPredicate("Hello world")));
Results results = searchService.search(classesWorkspace, new StringQuery(strMatchProvider.newStartsWithPredicate("Hello")));
Results results = searchService.search(classesWorkspace, new StringQuery(strMatchProvider.newEndsWithPredicate("world")));
```

All the available built-in predicates come from `StringPredicateProvider`, or you can provide your own predicate implementation.

### Number querying

```java
Results results = searchService.search(classesWorkspace, new NumberQuery(numMatchProvider.newEqualsPredicate(4)));
Results results = searchService.search(classesWorkspace, new NumberQuery(numMatchProvider.newAnyOfPredicate(6, 32, 256)));
Results results = searchService.search(classesWorkspace, new NumberQuery(numMatchProvider.newRangePredicate(0, 10)));
```

All the available built-in predicates come from `NumberPredicateProvider`, or you can provide your own predicate implementation.

### Reference querying

Each aspect of a reference *(declaring class, name, descriptor)* are their own string predicates. You pass `null` to any of these predicates to match anything for that given aspect. A simple example to find `System.out.println()` calls would look like:

```java
Results results = searchService.search(classesWorkspace, new ReferenceQuery(
         strMatchProvider.newEqualPredicate("java/lang/System"),     // declaring class predicate
         strMatchProvider.newEqualPredicate("out"),                  // reference name predicate
         strMatchProvider.newEqualPredicate("Ljava/io/PrintStream;") // reference descriptor predicate
));
```

If you want to find *all* references to a given package you could do something like this:

```java
Results results = searchService.search(classesWorkspace, new ReferenceQuery(
         strMatchProvider.newStartsWithPredicate("com/example/"),
         null, // match any field/method name
         null, // match any field/method descriptor
));
```

## Feedback handler

Passing a feedback handler to the `search`(...) methods allows you to control what classes and files are searched in by implementing the `doVisitClass(ClassInfo)` and `doVisitFile(FileInfo)` methods. Here is a basic example which limits the search to only classes in a given package:

```java
// All methods in the feedback interface default to visit everything, and include all results.
// You can override the 'boolean visitX' methods to control the searching of content within the passed classes/files.
class SkipClassesInPackage implements SearchFeedback {
    private final String pkg;
    
    SkipClassesInPackage(String pkg) { this.pkg = pkg; }
    
    @Override
    public boolean doVisitClass(@Nonnull ClassInfo cls) {
        // Skip if class does not exist in package
        return !cls.getName().startsWith(pkg);
    }
}
SearchFeedback skipping = new SkipClassesInPackage("com/example/");

```

To control the early abortion of a search you would implement `hasRequestedCancellation()` to return `true` after some point. A basic built in class can exists:

```java
// There is a built-in cancellable search implementation.
CancellableSearchFeedback cancellable = new CancellableSearchFeedback();

// Aborts the current search that this feedback is associated with.
cancellable.cancel();
```

To limit which results are included in the final `Results` of the `search(...)` call, implement`doAcceptResult(Result<?>)` to return `false` for results you want to discard. Since the `Result` contains a `PathNode` reference to where the match was made at, its probably what you'll want to operate on to implement your own filtering. Here is an example which limits the final `Results` to include only one item per class:

```java
// This is a silly example, but we really just want to show off how you'd implement this, not how to make a real-world implementation.
class OnlyOneResultPerClass implements SearchFeedback {
    private Set<String> includedClassNames = new HashSet<>();
    
    @Override
    public boolean doAcceptResult(@Nonnull Result<?> result) {
        PathNode<?> pathToValue = result.getPath();
        
        // Get the class value in the path to the value.
        // If the path points to something more specific like a instruction in a method, then
        // this will be the class that defines te method with that instruction in it.
        ClassInfo classPathValue = pathToValue.getValueOfType(ClassInfo.class);
        if (classPathValue != null && !includedClassNames.add(classPathValue.getName())) {
            // If we've already seen a result from this class, skip all the remaining results
            // so that there is only one result per class.
            return false;
        }
        
        // Keep the result in the output
        return true;
    }
}
```

