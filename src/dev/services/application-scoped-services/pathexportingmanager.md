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
pathExportingManager.export(workspace);
```

## Exporting a specific class/file

Specific `Info` types like `JvmClassInfo` and `FileInfo` can also be exported to user-provided paths:

```java
pathExportingManager.export(classInfo);
pathExportingManager.export(fileInfo);
```

