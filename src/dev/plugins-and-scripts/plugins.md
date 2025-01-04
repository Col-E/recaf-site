# Plugins

## What is a plugin?

A plugin is a JAR file that contains one or more classes with exactly one of them implementing `software.coley.recaf.plugin.Plugin`. When Recaf launches it looks in the plugins directory for JAR files that contain these plugin classes. It will then attempt to load and initialize them. Because a plugin is distributed as a JAR file a plugin developer can create complex logic and organize it easily across multiple classes in the JAR.

You can find a template project for creating plugins on GitHub at [Recaf-Plugins/Recaf-4x-plugin-workspace](https://github.com/Recaf-Plugins/Recaf-4x-plugin-workspace).

## Using services

Plugins can use services by annotating the class with `@Dependent` and annotating the constructor with `@Inject`. Here is an example:

```java
import jakarta.enterprise.context.Dependent;
import jakarta.inject.Inject;
import software.coley.recaf.plugin.*;
import software.coley.recaf.services.workspace.WorkspaceManager;

// Dependent is a CDI annotation which loosely translates to being un-scoped.
// Plugin instances are managed by Recaf so the scope is bound to when plugins are loaded in practice.
@Dependent
@PluginInformation(id = "##ID##", version = "##VERSION##", name = "##NAME##", description = "##DESC##")
class MyPlugin implements Plugin {
    private final WorkspaceManager workspaceManager;

    // Example, injecting the 'WorkspaceManager' service
    @Inject
    public MyPlugin(WorkspaceManager workspaceManager) {
        this.workspaceManager = workspaceManager;
    }

    @Override
    public void onEnable() { ... }

    @Override
    public void onDisable() { ... }
}
```

For the list of available services, see [the service lists](../services/index.html).

## CDI within Plugins

Plugins are capable of injecting Recaf's services in the plugin's constructor. Plugins themselves are only capable of being `@Dependent` scoped and cannot declare any injectable components themselves. For instance, if you want to create a new `JvmDecompiler` implementation that pulls in another Recaf service, you need to inject that service into the plugin's constructor and pass it to the `JvmDecompiler` implementation manually.

The reason for this is that once the CDI container is initialized at startup it cannot be modified. We can inject new classes with services already in the container, but nothing new can be added at runtime.

## Plugins and JavaFX

Plugins are loaded _before JavaFX initializes_. If your plugin has code that works with JavaFX classes or modifies Recaf's UI then you need to wrap that code in a `FxThreadUtil.run(() -> { ... })` call.

You can still inject most UI services directly like `MainMenuProvider`, but when calling its methods you have to be careful. There may be some services or injectable components that are a bit more finicky and will require `Instance<ThingIWantToInject>` instead to work around this, where you call `instance.get()` inside the `FxThreadUtil.run(() -> { ... })` call to get the instance when JavaFX has been initialized.