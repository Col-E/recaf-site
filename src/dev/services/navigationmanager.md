# NavigationManager

The nagivation manager allows you to:

* Lookup `Dockable` UI wrappers for `Navigable` UI content
* Add listeners to intercept addition/removal of `Navigable` content from the UI
  * Move operations fire a removal then addition


## Looking up dockables

The `Dockable` type represents a docking tab shown in the UI that holds some content. In this case the only kind of content that can be looked up is `Navigable` content. A `Navigable` UI element is one that represents something in the `Workspace` referencable by a `PathNode`. This would include classes, files, etc.

```java
Dockable dockable = navManager.lookupDockable(myNavigable);
if (dockable != null) {
    // TODO: Do things with the dockable
}
```

## Listening to navigable changes

Listening to when `Navigable` content lets you track what kinds of content is being displayed in Recaf.

```java
addNavigableAddListener(navigable -> {
    // You may want to react based on what kind of content is being added.
    if (navigable.getPath() instanceof ClassPathNode cp) {
        // TODO: Operate on removed navigable
    }
    
    // Another option for checking the content type, do an instanceof on the navigable.
    if (navigable instanceof ClassNavigable cn) {
        // ...
    } else if (navigable instanceof FileNavigable fn) {
        // ...
    }
});
addNavigableRemoveListener(navigable -> {
    // TODO: Operate on removed navigable
});
```