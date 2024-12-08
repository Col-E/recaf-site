# MappingApplierService

The mapping applier service takes in a `Mappings` instance and applies it to a `Workspace`, or a sub-set of specific classes in a `Workspace`.

## Targeting mappings in a workspace

Mappings can be applied to any `Workspace`. You will either pass a workspace reference to the applier service, or use convenience method for an applier in the currently open workspace.

```java
// Applier in an arbitrary workspace
Workspace workspace = ...
MappingApplier applier = mappingApplierService.inWorkspace(workspace);

// Applier in the current workspace (assuming one is open)
// If no workspace is open, this applier will be 'null'
MappingApplier applier = mappingApplierService.inCurrentWorkspace();
```

## Mapping the whole workspace

To apply mappings to the workspace *(affecting classes in the primary resource)* pass any `Mappings` of your choice to `applyToPrimaryResource(Mappings)`.

```java
Mappings mappings = ...

// Create the results containing the mapped output
MappingResults results = applier.applyToPrimaryResource(mappings);
```

## Mapping specific classes

To apply mappings to just a few specific classes, use `applyToClasses(Mappings, WorkspaceResource, JvmClassBundle, Collection<JvmClassInfo>)`.

```java
// Example inputs
Mappings mappings = ...
WorkspaceResource resource = ...
JvmClassBundle bundle = ... // A bundle in the resource
List<JvmClassInfo> classesToMap = ... // Classes in the bundle

// Create the results containing the mapped output
MappingResults results = applier.applyToClasses(mappings, resource, bundle, classesToMap);
```

## Operating on the results

The `MappingsResults` you get from `MappingsApplier` contains a summary of:

- The mappings used
- The classes that will be affected
  - The paths to classes in their existing state
  - The paths to classes in their post-mapping state

To apply the results call `apply()`.

```java
// Optional: Inspect the results
// =============================
// Names of affected classes: pre-mapped name --> post-mapped name
//  - If the class was updated because it contains a mapped reference but was itself not mapped
//    then the key/value are equal
Map<String, String> mappedClasses = results.getMappedClasses();
// Map of pre-mapped names to paths into the workspace of the class
Map<String, ClassPathNode> preMappingPaths = results.getPreMappingPaths();
// Map of post-mapped names to paths into the workspace at the location they
// would appear at after applying the results.
Map<String, ClassPathNode> postMappingPaths = results.getPostMappingPaths();

// Apply the mapping results (updates the workspace)
results.apply();
```

