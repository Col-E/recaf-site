# Plugins

## What is a plugin?

A plugin is a JAR file that contains one or more classes with exactly one of them implementing `software.coley.recaf.plugin.Plugin`. When Recaf launches it looks in the plugins directory for JAR files that contain these plugin classes. It will then attempt to load and initialize them. Because a plugin is distributed as a JAR file a plugin developer can create complex logic and organize it easily across multiple classes in the JAR.

## Using services

Plugins can use services by annotating the class with `@Dependent` and annotating the constructor with `@Inject`. For services that are application scoped, see this example:

```java
import jakarta.enterprise.context.Dependent;
import jakarta.inject.Inject;
import software.coley.recaf.plugin.*;
import software.coley.recaf.services.workspace.WorkspaceManager;

// Dependent is a CDI annotation which loosely translates to being un-scoped.
// Plugin instances are managed by Recaf so the scope is bound to when plugins are loaded in practice.
@Dependent
class MyPlugin implements Plugin {
    private final WorkspaceManager workspaceManager;

    // Example, injecting the 'WorkspaceManager' service which is '@ApplicationScoped'
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

For services that are `@WorkspaceScoped` they will not be available on plugin initialization since no `Workspace` will be set/opened when this occurs. You can use CDI constructs like `Instance<T>` and use them as getters to work around this. Calling `get()` on the instance after a `Workspace` is opened will grab the workspace-scoped instance of the desired service. As an example here is a basic plugin that injects the `InheritanceGraph` service:

```java
import jakarta.enterprise.context.Dependent;
import jakarta.enterprise.inject.Instance;
import jakarta.inject.Inject;
import software.coley.recaf.plugin.*;
import software.coley.recaf.services.inheritance.InheritanceGraph;
import software.coley.recaf.services.workspace.WorkspaceManager;

@Dependent
class MyPlugin implements Plugin {
    // We will use the workspace manager to listen to when new workspaces are opened.
    // When this occurs we can access instances of workspace scoped services.
    @Inject
    public MyPlugin(WorkspaceManager workspaceManager, 
                Instance<InheritanceGraph> graphProvider) {
        // No workspace open, wait until one is opened by the user.
        if (workspaceManager.getCurrent() == null) {
            workspaceManager.addWorkspaceOpenListener(newWorkspace -> {
                // This will be called AFTER the 'newWorkspace' value has been assigned
                // as the 'current' workspace in the workspace manager.
                // At this point, all workspace scoped services are re-allocated by CDI
                // to target the newly opened workspace.
                //
                // Thus, we can get our inheritance graph of the workspace here.
                InheritanceGraph graph = graphProvider.get();
            });
        } else {
            // There is a workspace, so we can immediately get the graph for the current workspace.
            InheritanceGraph graph = graphProvider.get();
        }
    }

    @Override
    public void onEnable() { ... }

    @Override
    public void onDisable() { ... }
}
```

<div class="hidden">TODO: Fix link to chapter index page - https://github.com/rust-lang/mdBook/issues/2060</div>

For the list of available services, see [the service lists](../services/index.html).