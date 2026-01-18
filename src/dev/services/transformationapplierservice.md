# TransformationApplierService

The transformation applier service allows you to take transformers registered in the [`TransformationManager`](transformationmanager.md) and apply them to the current workspace, or any arbitrary workspace.

## Creating a transformation applier

```java
// Will be null if there is no current workspace open
TransformationApplier applier = transformationApplierService.newApplierForCurrentWorkspace();

// If you want to repeat transformers multiple times, you can set a maximum pass count.
// This can be useful in some heavily obfuscated code where transformers can't get the work done in one pass.
// This count is a maximum; if transformers have nothing left to do, the process will not waste effort doing redundant passes.
applier.setMaxPasses(5);

// Will never be 'null' so long as the workspace variable is also not 'null'
// - The downside is that dependent features like the inheritance graph (for frame computation needed in some transformers)
//   will have to be generated each time you call this method since the applier will need access to that service for this workspace
//   that is not the "current" workspace.
// - If you happen to pass the "current" workspace as a parameter then this will delegate to the
//   more optimal 'newApplierForCurrentWorkspace()' method.
Workspace workspace = ...
TransformationApplier applier = transformationApplierService.newApplier(workspaqce);
```

## Applying transformations

```java
// Specify a list of transformers you want to apply.
List<Class<? extends JvmClassTransformer>> jvmTransformers = List.of(...);

// Optional: Feedback mechanism (an interface you implement) allows you to:
// - Cancel transformations at arbitrary times
// - Filter which classes get transformed
TransformationFeedback feedback = ...

applier.transformJvm(jvmTransformers); // To apply to all JVM classes in the workspace
applier.transformJvm(jvmTransformers, feedback); // To apply to classes matched by the feedback mechanism, and allow cancellation

// If the List<...> declaration is a big verbose you can inline it, and everything will play nice.
applier.transformJvm(List.of(...));
```

## Transformation Feedback

The `TransformationFeedback` interface lets you control which classes get transformed, cancel a transformation in-progress, and be notified of transformation progress. If you do not provide a feedback instance, a no-op implementation will be used which allows transformation of all classes.