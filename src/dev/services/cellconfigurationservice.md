# CellConfigurationService

The `CellConfigurationService` wraps cell-based UI services into convenient methods for use with `Cell` instances showing `PathNode` content.

The service allows you to:

* Configure a cell for a given `PathNode`, applying text, icon, and context menu
* Apply only the styling for a cell
* Resolve the text and graphic for a `PathNode`
* Build context menus for a `PathNode` given a `ContextSource`
* Produce click/context-menu handlers for tree cells

## Using it for custom cells

```java
// Configure a TreeCell to represent a class in the workspace
PathNode<?> item = // ...
cellConfigurationService.configure(cell, item, ContextSource.NONE);

// Configure just one aspect of the cell at a time:
cell.setText(cellConfigurationService.textOf(item));
cell.setGraphic(cellConfigurationService.graphicOf(item));
cell.setOnContextMenuRequested(cellConfigurationService.contextMenuHandlerOf(cell, item));
```
