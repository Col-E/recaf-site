# PathLoadingManager

The path loading manager controls loading `Workspace` instances in the UI. It has the following capabilities:

- Register listeners that intercept the `java.nio.Path` locations to user inputs before the `Workspace` is constructed from the paths.
- Asynchronously open a workspace from a `java.nio.Path` plus supporting resources from a list of `java.nio.Path` values.
- Asynchronously append supporting resources to a workspace from a list of `java.nio.Path` values.

## Intercepting user input paths

Registering a listener can allow you to see what file paths a user is requesting to load into Recaf.

```java
private final PathLoadingManager loadManager;

// ...

loadManager.addPreLoadListener((primaryPath, supportingPaths) -> {
    if (supportingPaths.isEmpty())
        logger.info("Loading workspace from {}", primaryPath);
    else
        logger.info("Loading workspace from {} + [{}]", primaryPath, 
            supportingPaths.stream().map(Path::toString).collect(Collectors.joining(", ")));
});
```

## Loading workspace content into Recaf asynchronously

As opposed to directly using `WorkspaceManager` this class handles things asynchronously since its intended for use in the UI. Here's how you can load a file as a workspace:

```java
private final PathLoadingManager loadManager;\

// ...

Path path = Paths.get("input.jar");
List<Path> supportingPaths = Arrays.asList(Paths.get("library-1.jar"), Paths.get("library-2.jar"));
CompletableFuture<Workspace> future = loadManager.asyncNewWorkspace(path, supportingPaths, 
        error -> logger.warn("Failed to load from '{}'", path));
```

Adding to the current workspace:

```java
Workspace workspace = workspaceManager.getCurrent();
List<Path> supportingPaths = Arrays.asList(Paths.get("library-1.jar"), Paths.get("library-2.jar"));
CompletableFuture<List<WorkspaceResource>> future = asyncAddSupportingResourcesToWorkspace(workspace, supportingPaths, 
        error -> logger.warn("Failed to append {}", supportingPaths.stream()
            .map(Path::toString)
            .collect(Collectors.joining(", "))));
```

