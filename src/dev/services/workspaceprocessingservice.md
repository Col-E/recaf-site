# WorkspaceProcessingService

The workspace processing service allows registering custom `WorkspaceProcessor`. These processors take in a `Workspace` parameter and can do anything.  They are applied to any workspace that gets opened via the [`WorkspaceManager`](workspacemanager.md).Generally these are intended to do lightweight operations such as the `ThrowablePropertyAssigningProcessor` which adds the `ThrowableProperty` to appropriate `ClassInfo` values in the workspace. For things more along the lines of bytecode manipulation you will want to check out the [`TransformationManager`](transformationmanager.md) and [`TransformationApplierService`](transformationapplierservice.md).

## Registering processors

```java
class MyProcessor implements WorkspaceProcessor {
    @Override
    public void processWorkspace(@Nonnull Workspace workspace) {
        // Processing goes here
    }
}

// Registering and unregistering
processingService.register(MyProcessor.class, () -> new MyProcessor());
processingService.unregister(MyProcessor.class);
```