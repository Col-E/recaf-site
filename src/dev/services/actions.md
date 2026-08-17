# Actions

The `Actions` service exposes common UI navigation operations so they can be triggered programmatically. Most, if not all, context menu actions are routed through here. It's a clear example of a [god class](https://en.wikipedia.org/wiki/God_object).

The service allows you to:

* Navigate to the declaration of a class, field, or method in their respective editor views.
* Open the assembler view of a class, field, or method.
* Open comment editing UIs for classes and members.
* Open the comments list and the area analysis views.
* Open the area analysis tool.
* Open the workspace summary view.
* Move classes/files via a package/directory selecting dialog.
* Move packages/directories via a package/directory selecting dialog.
* Rename classes, fields, methods, variables, files, packages, and directories with a name input dialog.
* Create new classes with a name input dialog.
* Copy an existing class/file/package/directory to a new location.
* Export selected classes/files/packages/directories to new archives.
* Delete classes/files/fields/methods/annotations with a selection dialog.
* Add fields / methods to a class.
* Open search views for strings, numbers, class references, class similarity search, member reference search, and instruction search.

Most operations take the workspace, resource, and bundle that own the target. These can be resolved from any path node you already have _(a search result, tree selection, etc)_:

```java
ClassPathNode path = // 
Workspace workspace = path.getValueOfType(Workspace.class);
WorkspaceResource resource = path.getValueOfType(WorkspaceResource.class);
JvmClassBundle bundle = path.getValueOfType(JvmClassBundle.class);
JvmClassInfo info = path.getValue();

// File content resolves the same way.
FilePathNode path = // ...
FileBundle bundle = filePath.getValueOfType(FileBundle.class);
FileInfo info = filePath.getValue();
```

## Navigating to a declaration

```java
// Look up a class by its internal name.
ClassPathNode classPath = workspace.findClass("com/example/Foo");

// Open the editor for a class, member, or file path.
// - By default this will be:
//    - Decompile view for classes, fields+methods will open the declaring class and select the member
//    - Text view for text files
//    - Audio/video player for audio/video files
//    - Hex view for binary files
ClassNavigable classNavigable = actions.gotoDeclaration(classPath);
Navigable memberNavigable = actions.gotoDeclaration(memberPath);
Navigable fileNavigable = actions.gotoDeclaration(filePath);
```

For more about navigable elements, see [NavigationManager](navigationmanager.md).

## Opening the assembler view

```java
// Class and member paths both work. The path just needs a class to assemble against.
actions.openAssembler(classPath);  // Whole-class assembler
actions.openAssembler(memberPath); // Single-member assembler
```

For more about the assembler, see [AssemblerPipelineManager](assemblerpipelinemanager.md).

## Editing + viewing comments

```java
// Comment for a class.
actions.openCommentEditing(workspace, resource, bundle, info);

// Comment for a field or method.
actions.openCommentEditing(workspace, resource, bundle, info, member);

// The comments list is workspace-wide.
actions.openCommentList();
```

For more about comments, see [CommentManager](commentmanager.md).

## Opening area analysis and summary views

```java
// Area analysis can be opened bare, scoped to a resource, or pre-seeded with a result.
actions.openAreaAnalysis();
actions.openAreaAnalysis(resource);
actions.openAreaAnalysis(resource, areaAnalysisResult);

// The workspace summary shows information about the current workspace.
// It is the page you see when first opening a file.
actions.openSummary();
```

## Moving classes, files, packages, and directories

Each `moveX` variant prompts for a destination, then relocates the item.

```java
actions.moveClass(workspace, resource, bundle, info);
actions.moveFile(workspace, resource, fileBundle, fileInfo);
actions.movePackage(workspace, resource, bundle, "com/example/old");
actions.moveDirectory(workspace, resource, fileBundle, "old/path");
```

## Renaming

The generic `rename` dispatches on the path type:

```java
actions.rename(classPath);     // ClassPathNode
actions.rename(memberPath);    // ClassMemberPathNode: field or method
actions.rename(variablePath);  // LocalVariablePathNode
actions.rename(filePath);      // FilePathNode
actions.rename(packagePath);   // DirectoryPathNode: package or directory
```

Or call the typed overloads directly:

```java
actions.renameClass(classPath);
actions.renameField(fieldPath);
actions.renameMethod(methodPath);
actions.renameVariable(variablePath);
actions.renameFile(filePath);
actions.renamePackageOrDirectory(directoryPath);
```

For more about mapping, see [MappingApplierService](mappingapplierservice.md).

## Creating new classes

```java
// Prompts for a class name, then creates and opens the new class in the given bundle under the given package.
actions.newClass(workspace, resource, bundle, "com/example/newpkg");
```

## Copying

```java
// Prompts for a target name of the class/member/file/etc.
//  - Classes/Files will create a entry in the workspace.
//  - Members will update an existing entry for a class when copying fields/methods.
//  - Packages/Directories will create new entries in the workspace for all copied items with their new parent package/directory.
actions.copyClass(workspace, resource, bundle, info);
actions.copyMember(workspace, resource, bundle, info, member);
actions.copyFile(workspace, resource, fileBundle, fileInfo);
actions.copyPackage(workspace, resource, bundle, "com/example/old");
actions.copyDirectory(workspace, resource, fileBundle, "old/path");
```

## Exporting

```java
// Export single '.class' file.
actions.exportClass(workspace, resource, bundle, info);

// Export multiple classes from a package/bundle to a '.jar/zip' file.
actions.exportPackage(workspace, resource, bundle, "com/example/old");
actions.exportClasses(workspace, resource, bundle);

// Export multiple files from a directory/bundle to a '.zip' file.
actions.exportDirectory(workspace, resource, fileBundle, "old/path");
actions.exportFiles(workspace, resource, fileBundle);
```

For more about exporting, see [PathExportingManager](pathexportingmanager.md).

## Deleting

```java
// Remove contents from the workspace.
actions.deleteClass(workspace, resource, bundle, info);
actions.deleteFile(workspace, resource, fileBundle, fileInfo);
actions.deletePackage(workspace, resource, bundle, "com/example/old");
actions.deleteDirectory(workspace, resource, fileBundle, "old/path");

// Members and annotations on a class can also be deleted.
// This will show a dialog asking the user which members/annotations to delete.
actions.deleteClassFields(workspace, resource, bundle, info);
actions.deleteClassMethods(workspace, resource, bundle, info);
actions.deleteClassAnnotations(workspace, resource, bundle, info);
actions.deleteMemberAnnotations(workspace, resource, bundle, info, member);
```

To remove an annotation without any prompt there is `immediateDeleteAnnotations(bundle, annotated, annotationType)`.

## Adding members

```java
// Prompts for a new field / method and adds it to the class.
actions.addClassField(workspace, resource, bundle, info);
actions.addClassMethod(workspace, resource, bundle, info);

// Prompts to select a method to override.
actions.overrideClassMethod(workspace, resource, bundle, info);
```

## Opening search views

The `openNewX` methods return the created search pane, docked in the search area.

```java
StringTablePane stringTable = actions.openNewStringTable();
StringSearchPane stringSearch = actions.openNewStringSearch();
NumberSearchPane numberSearch = actions.openNewNumberSearch();
ClassReferenceSearchPane classRefSearch = actions.openNewClassReferenceSearch();
MemberReferenceSearchPane memberRefSearch = actions.openNewMemberReferenceSearch();
MemberDeclarationSearchPane memberDeclSearch = actions.openNewMemberDeclarationSearch();
InstructionSearchPane instructionSearch = actions.openNewInstructionSearch();

// You can then operate on these yielded panes to fill in fields. Here's an example used when selecting 'Field references':
MemberReferenceSearchPane pane = actions.openNewMemberReferenceSearch();
pane.ownerPredicateIdProperty().setValue(StringPredicateProvider.KEY_EQUALS);
pane.namePredicateIdProperty().setValue(StringPredicateProvider.KEY_EQUALS);
pane.descPredicateIdProperty().setValue(StringPredicateProvider.KEY_EQUALS);
pane.ownerValueProperty().setValue(declaringClass.getName());
pane.nameValueProperty().setValue(field.getName());
pane.descValueProperty().setValue(field.getDescriptor());
```

Similarity searches take a reference path instead of starting empty:

```java
SimilarClassTablePane similarClasses = actions.openSimilarClassSearch(classPath);
SimilarMethodTablePane similarMethods = actions.openSimilarMethodSearch(methodPath);
```

For more about searching, see [SearchService](searchservice.md).
