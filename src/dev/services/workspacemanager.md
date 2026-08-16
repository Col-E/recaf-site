# WorkspaceManager

The attach manager allows you to:

- Access the current workspace
- Set the current workspace
- Add listeners to be notified of:
  - New workspaces being opened
  - Workspaces being closed
  - Changes to existing workspaces *(The model, not the content)* being made

## Accessing the current workspace

The current workspace is accessed via `getCurrent()`. 

```java
// The return value is never 'null' - if no workspace is open you get a 'EmptyWorkspace'
Workspace workspace = workspaceManager.getCurrent();
```

## Setting the workspace

Assigning a workspace is done via `setCurrent(Workspace)`. You can "unset" or close a workspace by passing `null` or by calling `closeCurrent()`.

```java
Workspace workspace = // ..
workspaceManager.setCurrent(workspace);

// These two calls behave the same
workspaceManager.closeCurrent();
workspaceManager.setCurrent(null);
```

In the case where a `WorkspaceCloseCondition` has been registered the request to close a workspace can be blocked. Consider that when you are using the GUI and you close a file you are asked _"Are you sure?"_ before closing the workspace. To ensure any potential cause of closing the workspace is handled this is achieved by a registering a `WorkspaceCloseCondition` in the UI which requires answering the prompt before allowing the close to occur.

While it is _not recommended_ you can circumvent such conditions by using `setCurrentIgnoringConditions(Workspace)` instead of `setCurrent(Workspace)`.

## Listening for new workspaces

Register a `WorkspaceOpenListener`. 

```java
workspaceManager.addWorkspaceOpenListener(workspace -> {
    // Operate on newly opened workspace
});
```

## Listening to workspace closures

Register a `WorkspaceCloseListener`. Mostly useful for read-only handling such as logging.

```java
workspaceManager.addWorkspaceCloseListener(workspace -> {
    // Operate on closed workspace
});
```

Similarly you can have a `WorkspaceCloseCondition` if you want to listen to and prevent workspace closures.

```java
workspaceManager.addWorkspaceCloseCondition(workspace -> {
    // Returning 'false' will prevent a workspace from being closed.
    if (shouldPreventClosure(workspace)) return false;
    
    return true;
});
```

## Listening to workspace structure modifications

Normally you would add a `WorkspaceModificationListener` on a specific `Workspace` but in the `WorkspaceManager` you can add a _"default"_ `WorkspaceModificationListener` which is added to all newly opened workspaces.

```java
workspaceManager.addDefaultWorkspaceModificationListeners(new WorkspaceModificationListener() {
    @Override
    public void onAddLibrary(@Nonnull Workspace workspace, @Nonnull WorkspaceResource library) {
        // Supporting library added to workspace
    }
    @Override
    public void onRemoveLibrary(@Nonnull Workspace workspace, @Nonnull WorkspaceResource library) {
        // Supporting library removed from workspace
    }
});
```