# TextProviderService

The text provider service allows you to override text shown for workspace contents like classes, fields, methods, etc.

## Getting text

Text is computed lazily by `TextProvider` instances. Each workspace content type has its own provider you can get by passing in parameters to reconstruct a `PathNode` to the respective content. In the case of a bundle, that would be the `Workspace`, `WorkspaceResource`, and `Bundle`.

```java
TextProvider provider = textService.getBundleTextProvider(workspace, resource, bundle);
```