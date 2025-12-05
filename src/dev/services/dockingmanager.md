# DockingManager

The docking manager allows you to:

* Get the underlying `Bento` instance
* Get the root bento container that holds the docking container hierarchy
* Get the primary bento container that holds open `Navigable` contents _(Where classes and other files get opened by default)_
* Easily create new `Dockable` instance

## Getting Bento

Its just a getter. That being said, the `Bento` instance has a lot you can do with it. It has its own event bus _(Which you can add listeners to)_, search API, and hooks for creating UI elements. See [BentoFX](https://github.com/Col-E/BentoFX) for additional usage.

```java
Bento bento = dockManager.getBento();
```

## Getting root/primary containers

The root container is the container that houses all others in the initial Recaf window.

```java
DockContainerRootBranch root = dockManager.getRoot();
```

The primary container is the container that houses `Dockable` items represening classes and files you open. Its the initial space they will open in when interacting with them.

```java
DockContainerLeaf primary = dockManager.getPrimaryDockingContainer();
```

## Creating dockables

Dockables are the bento model for open tabs. The docking manager provides a number of helper methods for creating common sorts of dockables.