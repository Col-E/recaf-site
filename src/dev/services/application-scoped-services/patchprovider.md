# PatchProvider

The patch provider facilitates the creation of `WorkspacePatch` instances.

## Generating patches from changes in a workspace

A patch that represents all the changes made to a workspace _(Removing files, editing classes, etc)_ can be made by calling `createPath(Workspace)`.

```java
Workspace workspace = ...
// Some changes to the workspace are made...
// Generate a patch that represents the changes
WorkspacePatch patch = patchProvider.createPatch(workspace);
```

## Reading/writing patches from JSON

Patches can be persisted to a JSON representation via `serializePatch(WorkspacePatch)` and `deserializePatch(Workspace, String)`.

```java
// Given a 'WorkspacePatch' transform it into JSON.
String serializedJson = patchProvider.serializePatch(patch);

// Given some JSON transform it back into a patch.
// We pass along the workspace that this patch will be applied to.
WorkspacePatch deserializePatch = patchProvider.deserializePatch(workspace, serializedJson);
```

## Applying patches

See [PatchApplier](patchapplier.md)