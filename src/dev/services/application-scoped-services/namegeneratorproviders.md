# NameGeneratorProviders

The `NameGeneratorProviders` service allows you to:

-  See which `NameGeneratorProvider` are available
- Register your own `NameGeneratorProvider`

## Get current name providers

```java
// Read-only map of generator ids to generator provider instances
Map<String, NameGeneratorProvider<?>> providers = nameGeneratorProviders.getProviders();
```

## Registering a new `NameGeneratorProvider`

```java
// AbstractNameGeneratorProvider implements most things for you.
// All that you need to do is pass a unique 'id' in the constructor and implement 'createGenerator()'
nameGeneratorProviders.registerProvider(new AbstractNameGeneratorProvider<>("my-unique-id") {
    @Nonnull
    @Override
    public NameGenerator createGenerator() {
        // Create your name generator here
    }
});
```