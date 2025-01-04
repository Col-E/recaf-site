# ScriptManager

The script manager tracks recognized scripts in the Recaf scripts directory. It can also be used to parse arbitrary `java.nio.Path` items into `ScriptFile` instances.

## Local scripts

In the Recaf root directory a sub-directory named `scripts` is watched for changes. Any files found in this directory will be checked for being valid scripts and recorded in this manager if they match. You can access these scripts and even listen for when scripts are added and removed via `getScriptFiles()` which returns an `ObservableCollection` of `ScriptFile`s.

```java
// Iterating over the currently known scripts
for (ScriptFile script : scriptManager.getScriptFiles()) {
    // ...
}

// Listening for changes in local scripts
scriptManager.getScriptFiles().addChangeListener((ob, oldScriptList, newScriptList) -> {
    // The files changed between the old and new list instances
    List<ScriptFile> disjoint = Lists.disjoint(oldScriptList, newScriptList);
});
```

## Reading files as scripts

To parse a script from a file path call `read(Path)`:

```java
ScriptFile script = scriptManager.read(Paths.get("Example.java"));
String content = script.source();
String metadataName = script.name(); // Meta-data not specified in the script file will yield an empty string
String metadataDesc = script.description();
String metadataVersion = script.version();
String metadataAuthor = script.author();
String metadataCustom = script.getTagValue("custom");
```

See the [scripting section](../../plugins-and-scripts/scripts.md) for more information about the contents of script files.