# ContextMenuProviderService

The context menu provider service allows you to inject custom contents into context menus for content in the workspace such as classes, fields, methods, etc. This allows plugins to display whatever they want in the menus shown when a user right clicks on these contents within Recaf.

## Registering custom context menu adapters

The service has a dozen `addX` and `removeX` methods for context menu adapters of various types _(classes, fields, methods, etc)_. Most can be implemented as lambdas, though this does prevent you from later removing the adapter.

```java
// Most adapters are single-method-interfaces and can be expressed as lambdas.
MethodContextMenuAdapter adapter = (menu, source, workspace, resource, bundle, declaringClass, method) -> {
   menu.getItems().add(new MenuItem("Custom item for: " + method.getName()));
};

// Add / remove the adapter
ctxMenuService.addMethodContextMenuAdapter(adapter);
ctxMenuService.removeMethodContextMenuAdapter(adapter);
```

Another example for classes, which are a bit more complex since they differentiate between JVM and Android classes.

```java
// Classes are a bit special because they differentiate between JVM and Android classes.
// You can of course just pipe both into a method with more generic argument types (see below)
ClassContextMenuAdapter adapter = new ClassContextMenuAdapter() {
   @Override
   public void adaptJvmClassMenu(@Nonnull ContextMenu menu, 
                                 @Nonnull ContextSource source, 
                                 @Nonnull Workspace workspace,
                                 @Nonnull WorkspaceResource resource,
                                 @Nonnull JvmClassBundle bundle,
                                 @Nonnull JvmClassInfo info) {
      common(menu, source, workspace, resource, bundle, info);
   }

   @Override
   public void adaptAndroidClassMenu(@Nonnull ContextMenu menu,
                                     @Nonnull ContextSource source,
                                     @Nonnull Workspace workspace,
                                     @Nonnull WorkspaceResource resource,
                                     @Nonnull AndroidClassBundle bundle,
                                     @Nonnull AndroidClassInfo info) {
      common(menu, source, workspace, resource, bundle, info);
   }

   private void common(@Nonnull ContextMenu menu,
                       @Nonnull ContextSource source, 
                       @Nonnull Workspace workspace,
                       @Nonnull WorkspaceResource resource, 
                       @Nonnull ClassBundle<?> bundle,
                       @Nonnull ClassInfo info) {
      // More generic types, can do common logic between JVM and Android content here
   }
};

// Add / remove the adapter
ctxMenuService.addClassContextMenuAdapter(adapter);
ctxMenuService.removeClassContextMenuAdapter(adapter);
```