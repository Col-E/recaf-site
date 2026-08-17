# ConfigComponentManager

The `ConfigComponentManager` creates the JavaFX components used to edit config values in the config window. It allows registering custom editors for specific config value types, or for specific keys of a container.

The manager allows you to:

* Register a factory for a specific config value type
* Register a factory for a specific config value ID within a container
* Look up the factory for any given `ConfigValue`

## Registering a custom editor

```java
// Factory for a specific value type.
configComponentManager.register(MyType.class, (container, value) -> new MyTypeEditor(value));

// Factory for a specific key of a specific container.
configComponentManager.register(myConfigContainer, "my-value-id", (container, value) -> new MyValueEditor(value));

// Build the editor node for a value.
// This is what we do in the config window.
Node editorNode = configComponentManager.getFactory(container, value).create(container, value);
```
