# WindowManager

The `WindowManager` tracks the open windows of the application by identifier, and provides access to the well-known windows.

The service allows you to:

* Register and unregister windows by identifier.
* Register anonymous windows that are not tracked by identifier.
* Look up windows by identifier.
* Access the well-known windows directly.
  * Main
  * Remote VMs
  * Config
  * System information
  * Script manager
  * Mapping progress preview
  * Quick nav


## Looking up windows

```java
// Well-known windows
Stage main = windowManager.getMainWindow();
Stage config = windowManager.getConfigWindow();
Stage scripts = windowManager.getScriptManagerWindow();

// Any registered window by ID
Stage custom = windowManager.getWindow("my-window-id");

// Iterate all open windows
for (Stage window : windowManager.getActiveWindows())
    ...
```
