# PluginManager

The plugin manager handles loading, unloading, and tracking of plugins. See [Plugins](../plugins-and-scripts/plugins.md) for the user-facing plugin documentation.

The manager allows you to:

* Look up loaded plugins by their ID
* Iterate over all loaded plugins, or all plugins of a given type
* Load plugins from a `PluginDiscoverer`
* Register custom `PluginLoader` implementations
* Obtain an unloader for a plugin

## Looking up plugins

```java
// By ID, or null if not loaded
PluginContainer<MyPlugin> container = pluginManager.getPlugin("my-plugin-id");

// All plugins
Collection<PluginContainer<?>> plugins = pluginManager.getPlugins();

// All plugins of a specific type
Collection<MyPlugin> typedPlugins = pluginManager.getPluginsOfType(MyPlugin.class);
```

## Loading plugins

```java
// Discover + load plugins from a directory
PluginDiscoverer discoverer = new DirectoryPluginDiscoverer(Paths.get("./plugins"));
Collection<PluginContainer<?>> loaded = pluginManager.loadPlugins(discoverer);

// Unload a plugin when done
PluginUnloader unloader = pluginManager.unloaderFor("my-plugin-id");
unloader.commit();
```
