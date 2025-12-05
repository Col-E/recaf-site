# AstService

The AST service allows you to:

* Parse Java source code into an AST model
* Resolve what is at a given offset in the source code based on the AST model
* Transform Java source to apply name mappings

## Parsing Java source

To parse Java source you need a `Parser` (See: [SourceSolver](https://github.com/Col-E/SourceSolver)).

```java
// Create a new parser instance
Parser parser = astService.newParser();

// Or use the shared parser used by other Recaf services
parser = astService.getSharedJavaParser();
```

Once you have a parser, you can turn it into a `CompilationUnitModel` _(The AST model)_ via `astService.parseJava(parser, src)` where `src` is a `String` containing your Java source code.

```java
CompilationUnitModel model = astService.parseJava(parser, src)
```

## Resolving content in Java sources

Once you have a `CompilationUnitModel` you'll create a `ResolverAdapter` which can be used later to determine what kind of content is at a given text offset.

```java
// The single arguument 'newJavaResolver' will create a resolver using type information loaded from the current open workspace.
ResolverAdapter resolver = astService.newJavaResolver(model);

// Get the text offset of a method call in the original 'String src'
int target = src.indexOf("someMethodCall(");
AstResolveResult result = resolver.resolveThenAdapt(target);

// Skip if a result was not yielded.
if (result == null) return;

// You can operate on if the content at the given offset is a declaration in the source code, or a reference to another declaration.
// It operates in the same way that right clicking in Recaf does (this is the backend for that actually). 
//  - "private int example" --> offset of "example" --> Declaration of the field "example"
//  - "src.isEmpty()" --------> offset of "isEmpty" --> Reference to the method "isEmpty"
if (result.isDeclaration()) {
    // ...
} else {
    // ...    
}

// Regardless of whether the result is a declaration or reference, you can operate on the path to the declaration.
PathNode<?> path = result.path();
```

## Transforming name mappings

Assuming you have the parsed `CompilationUnitModel`, a `Mappings` instance with some entries, and a `Resolver` instance _(Satisfied by `ResolverAdapter` seen above)_ you can generate Java source code with name mapping applied. Generally you should be doing mappings on the bytecode, but this is an option.

```java
Mappings mappings = // ... (load or generate mappings however you please)
String mappedSrc = astService.applyMappings(model, resolver, mappings);
```

