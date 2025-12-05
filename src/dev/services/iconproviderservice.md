# IconProviderService

The icon provider service allows you to get and override icons shown for workspace contents like classes, fields, methods, etc.

## Getting icons

Icons are fetched lazily by `IconProvider` instances. Each workspace content type has its own provider you can get by passing in parameters to reconstruct a `PathNode` to the respective content. In the case of a bundle, that would be the `Workspace`, `WorkspaceResource`, and `Bundle`.

```java
IconProvider provider = iconService.getBundleIconProvider(workspace, resource, bundle);
```

## Replacing icons

To replace icons for different sorts of contents, use `setXOverride` in the service for any content type of `X`.

```java
// Example overriding bundle icons
iconService.setBundleIconProviderOverride(new BundleIconProviderFactory() {
    @Override
    @Nonnull
    public IconProvider getBundleIconProvider(@Nonnull Workspace workspace,
                                              @Nonnull WorkspaceResource resource, 
                                              @Nonnull Bundle<? extends Info> bundle) {
        // Icon providers lazily provide icons (As JavaFX 'Node' instances) 
        // and are generally defined as lambdas like this.
        return () -> new FontIconView(CarbonIcons.SHOPPING_BAG, Color.LIME);
    }
});
```