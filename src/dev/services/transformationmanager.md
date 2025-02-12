# TransformationManager

The transformation manager allows registering of different transformer types for class processing operations.

## Example Transformer

```java
public class MyTransformer implements JvmClassTransformer {
    /** Optional, can delete if you do not need any one-time setup logic */
    @Override
    public void setup(@Nonnull JvmTransformerContext context, @Nonnull Workspace workspace) {
        // Transformation setup here
        //  - Pulling values from the context
        //  - Checking contents of the workspace
        //  - Etc.
    }

    /** Used to transform the classes */
    @Override
    public void transform(@Nonnull JvmTransformerContext context, @Nonnull Workspace workspace,
                          @Nonnull WorkspaceResource resource, @Nonnull JvmClassBundle bundle,
                          @Nonnull JvmClassInfo initialClassState) throws TransformationException {
        if (exampleOfRawEdit) {
            // IMPORTANT: Use this method to get the bytecode, and DO NOT use the direct 'initialClassState.getBytecode()'!
            // The context will store updated bytecode across multiple transformations, so if you use the direct
            // bytecode from the 'ClassInfo' you risk losing all previous transform operations.
            byte[] modifiedBytecode = context.getBytecode(bundle, initialClassState);
            
            // TODO: Make changes to 'modifiedBytecode' here
            
            // Save modified bytecode into the context.
            context.setBytecode(bundle, initialClassState, modifiedBytecode);
        } else if (exampleOfAsmTreeEdit) {
            // IMPORTANT: The same note as above applies here, but with ASM's ClassNode.
            ClassNode node = context.getNode(bundle, initialClassState);
            
            // TODO: Make changes to 'node' here
            
            // Save modified class-node (and its respective bytecode) into the context.
            context.setNode(bundle, initialClassState, node);
            
            // If the changes are significant and require recomputing stack-frames, calling this will ensure
            // this process is done for you automatically after all transformers are applied.
            context.setRecomputeFrames(initialClassState.getName());
        }
    }

    /** Unique name of this transformer */
    @Nonnull
    @Override
    public String name() {
        return "My cool transformer";
    }

    /** Any dependencies this transformer relies on. Some transformers are used for analysis and store data
     *  that can be accessed later, and depending on those transformers ensures the data is accessible when
     *  this transformer is invoked. */
    @Nonnull
    @Override
    public Set<Class<? extends ClassTransformer>> dependencies() {
        // Optional method, you can delete this if you have no dependencies.
        // But if you do use dependencies, you can get instances of them via 'context.getJvmTransformer(OtherTransformer.class)'
        // in the 'setup' and 'transform' methods above.
        return Collections.emptySet();
    }
}
```

## Registering transformers

```java
// Registering and unregistering
processingService.registerJvmClassTransformer(MyTransformer.class, () -> new MyTransformer());
processingService.unregisterJvmClassTransformer(MyTransformer.class);
```