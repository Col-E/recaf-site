# TransformationApplierService

The transformation applier service allows you to take transformers registered in the [`TransformationManager`](transformationmanager.md) and apply them to the current workspace, or any arbitrary workspace.

## Creating a transformation applier

```java
// Will be null if there is no current workspace open
TransformationApplier applier = transformationApplierService.newApplierForCurrentWorkspace();

// Will never be 'null' so long as the workspace variable is also not 'null'
// - The downside is that dependent features like the inheritance graph (for frame computation needed in some transformers)
//   will have to be generated each time you call this method since the applier will need access to that service for this workspace
//   that is not the "current" workspace.
// - If you happen to pass the "current" workspace as a parameter then this will delegate to the
//   more optimal 'newApplierForCurrentWorkspace()' method.
Workspace workspace = ...
TransformationApplier applier = transformationApplierService.newApplier(workspaqce);
```