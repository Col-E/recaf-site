# AssemblerPipelineManager

The assembler pipeline manager is the entry point for Recaf's assembler and disassembler implementations. It is initialized eagerly at startup and provides access to both the JVM and Dalvik assembler pipelines.

The manager allows you to:

* Get the assembler pipeline suitable for the content at a given path
* Create a fresh `JvmAssemblerPipeline` for a workspace
* Create a fresh `AndroidAssemblerPipeline` for a workspace

## Getting a pipeline

```java
// Pick the pipeline that matches the content at the given path.
// Returns either a JvmAssemblerPipeline or an AndroidAssemblerPipeline.
AssemblerPipeline<?, ?, ?> pipeline = assemblerPipelineManager.getPipeline(path);

// Or create one directly for the workspace.
JvmAssemblerPipeline jvmPipeline = assemblerPipelineManager.newJvmAssemblerPipeline(workspace);
AndroidAssemblerPipeline dalvikPipeline = assemblerPipelineManager.newAndroidAssemblerPipeline(workspace);
```

Pipelines operate on `ASTElement` lists produced by [Jasm's](https://github.com/jumanji144/Jasm) parser, so the typical flow is to parse text into AST elements and then assemble them against a path in the workspace:

```java
// Disassemble an existing class/member to text
Result<String> text = jvmPipeline.disassemble(classPath);

// Assemble parsed elements onto a target path
Result<JavaCompileResult> result = jvmPipeline.assemble(parsedElements, classPath);
```

## Disassembling

For disassembling a whole class, you call `jvmPipeline.disassemble(path)` with a `ClassPathNode`.

```java
@Nonnull
protected String disassemble(@Nonnull JvmClassInfo cls) {
    JvmClassBundle bundle = workspace.getPrimaryResource().getJvmClassBundle();
    WorkspaceResource resource = workspace.getPrimaryResource();
    ClassPathNode path = PathNodes.classPath(workspace, resource, bundle, cls);
    Result<String> disassembly = jvmPipeline.disassemble(path);
    if (disassembly.isOk())
        return disassembly.get();
    return "<error>";
}
```

For disassembling a single member in a class you call the same method, but with a `ClassMemberPathNode`.

```java
@Nonnull
protected String disassemble(@Nonnull JvmClassInfo cls, @Nonnull String name) {
    JvmClassBundle bundle = workspace.getPrimaryResource().getJvmClassBundle();
    WorkspaceResource resource = workspace.getPrimaryResource();
    MethodMember method = cls.getFirstDeclaredMethodByName(name); // You can also do name/desc lookup
    ClassMemberPathNode path = PathNodes.memberPath(workspace, resource, bundle, cls, method);
    Result<String> disassembly = jvmPipeline.disassemble(path);
    if (disassembly.isOk())
        return disassembly.get();
    return "<error>";
}
```

## Assembling

Assembling is a two-phase process: parse the JASM text into AST elements, then compile those elements against a workspace path. Both `assemble` and `assembleAndWrap` do not modify the workspace on their own, you are responsible for writing the resulting class back into its bundle.

### Parsing text to AST

```java
// 'fullParse' does the rough + concrete parse steps in one call.
Result<List<ASTElement>> parsed = jvmPipeline.tokenize(text, "<assembly>")
        .flatMap(jvmPipeline::fullParse);
```

### Assembling a whole class

When the path points at a class, the parsed input must be a single class declaration.

```java
WorkspaceResource resource = workspace.getPrimaryResource();
JvmClassBundle bundle = resource.getJvmClassBundle();
JvmClassInfo cls = bundle.get("com/example/Foo");
ClassPathNode classPath = PathNodes.classPath(workspace, resource, bundle, cls);

// 'assembleAndWrap' compiles the AST and converts the result into a Recaf class.
Result<JvmClassInfo> result = jvmPipeline.tokenize(text, "<assembly>")
        .flatMap(jvmPipeline::fullParse)
        .flatMap(ast -> jvmPipeline.assembleAndWrap(ast, classPath));

// Store the assembled class back into the workspace.
result.ifOk(assembled -> bundle.put(assembled))
        .ifErr(errors -> { /* surface errors */ });
```

If you need the raw compile result instead, `assemble` returns a `Result<JavaCompileResult>` whose `representation()` can be converted with `jvmPipeline.getClassInfo(...)`.

### Assembling a single member

Editing one method or field means assembling a lone member element rather than a whole class. Just pass the member's path to `assembleAndWrap` and the pipeline overlays the existing class representation so the member compiles in the context of its declaring class.

```java
MethodMember method = cls.getFirstDeclaredMethodByName("example");
ClassMemberPathNode memberPath = PathNodes.memberPath(workspace, resource, bundle, cls, method);

Result<JvmClassInfo> result = jvmPipeline.tokenize(methodText, "<assembly>")
        .flatMap(jvmPipeline::fullParse)
        .flatMap(ast -> jvmPipeline.assembleAndWrap(ast, memberPath)); // <-- ClassMemberPathNode vs ClassPathNode

// The result is still the full class, with the member changes applied.
result.ifOk(assembled -> bundle.put(assembled))
        .ifErr(errors -> { /* surface errors */ });
```

### Updating the workspace

In both cases `bundle.put(assembled)` replaces the previous definition of the class _(same name, new bytecode)_. If you are holding a path that may be stale, look up the class's current form in the workspace first. You can do this via `workspace.findClass(...)` before assembling so it builds against the latest state instead of an outdated snapshot.
