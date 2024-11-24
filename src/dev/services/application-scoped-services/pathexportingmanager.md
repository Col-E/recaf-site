# PathExportingManager

The path exporting manager facilitates exporting various workspace types to files, prompting the user to provide locations to save to

## Exporting the current workspace

The currently open workspace can be exported to a user-provided path like so:

```java
try { pathExportingManager.exportCurrent(); }
catch (IllegalStateException ex) { /* no workspace open */ }
```

## Exporting a specific workspace

Any workspace instance can also be exported:

```java
// Delegates to export(workspace, "workspace", true)
pathExportingManager.export(workspace);

// Prompts the user to:
// - Alert them if the workspace has no changes recorded in it (seeL alertWhenEmpty)
// - Provide a location to save the workspace to (if you loaded a jar, you should probably provide a location like "export.jar")
boolean alertWhenEmpty = false;
String description = "some files"; // Used in logger output so that we see "exported 'some files' to %PATH%"
pathExportingManager.export(workspace, description, alertWhenEmpty);
```

## Exporting a specific class/file

Specific `Info` types like `JvmClassInfo` and `FileInfo` can also be exported to user-provided paths:

```java
pathExportingManager.export(classInfo);
pathExportingManager.export(fileInfo);
```

