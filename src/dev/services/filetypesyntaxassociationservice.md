# FileTypeSyntaxAssociationService

The `FileTypeSyntaxAssociationService` associates file types with syntax highlighting configurations for our `Editor` class. The associations are user-configurable in the UI.

The service allows you to:

* Configure an `Editor`'s syntax highlighting based on an `Info` value _(file content)_
* Configure an `Editor`'s syntax highlighting based on a file extension

## Configuring an editor

```java
// Automatic inferred by info type:
//  - ClassInfo -> java
//  - BinaryXmlFileInfo -> xml
//  - FileInfo -> getExtension(info.getName())
fileTypeSyntaxAssociationService.configureEditorSyntax(info, editor);

// Manually assigning type:
fileTypeSyntaxAssociationService.configureEditorSyntax("json", editor);
```

For available syntaxes see: [`recaf-ui/src/main/resources/syntax`](https://github.com/Col-E/Recaf/tree/master/recaf-ui/src/main/resources/syntax).
