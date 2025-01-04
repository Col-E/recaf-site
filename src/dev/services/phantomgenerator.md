# PhantomGenerator

The phantom generator service allows you to create phantoms for:

* Entire workspaces at a time
* One or more specific classes from a workspace

## Generating phantoms

For generating phantoms for all primary classes in a workspace:

```java
@Inject
PhantomGenerator phantomGenerator;

@Inject
Workspace workspace; // Injected in this example to pull in the 'current' workspace, but it can be any arbitrary workspace

// Most common use case is to then append the phantoms to the workspace 
// so they can be used to suplement other services operating off of the current workspace.
GeneratedPhantomWorkspaceResource phantomResource = phantomGenerator.createPhantomsForWorkspace(workspace);
workspace.addSupportingResource(phantomResource)
```

For generating phantoms for just a few classes:

```java
List<JvmClassInfo> classes = workspace.findJvmClasses(c -> c.getName().startsWith("com/example")).stream()
     .map(path -> path.getValue().asJvmClass())
     .toList();

// Phantoms in the output will only be generated to satisfy missing references in the 'classes' we pass.
GeneratedPhantomWorkspaceResource phantomResource = phantomGenerator.createPhantomsForClasses(workspace, classes);
```