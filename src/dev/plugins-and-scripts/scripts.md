# Scripts

## What is a script?

A script is a single Java source file that is executed by users whenever they choose. They can be written as a _full class_ to support similar capabilities to plugins such as service injection, or in a _short-hand_ that offers automatic imports of most common utility classes, but no access to services.

### Full class script

A full class script is just a regular class that defines a non-static void `run()`. The `run()` method is called whenever the user executes the script.

```java
// ==Metadata==
// @name Hello world
// @description Prints hello world
// @version 1.0.0
// @author Author
// ==/Metadata==

class MyScript {
    // must define 'void run()'
    void run() {
        System.out.println("hello");
    }
}
```

You can access any of Recaf's services by declaring a constructor annotated with `@Inject`. More information on this is located further down the page.

### Shorthand script

A shorthand script lets you write your logic without needing to declare a class and `run()` method. These shorthand scripts are given a variable reference to the current workspace, and a SLF4J logger. You can access the current workspace as `workspace` and the logger as `log`.

```java
// ==Metadata== 
// @name What is open?
// @description Prints what kinda workspace is open
// @version 1.0.0
// @author Author
// ==/Metadata==

String name = "(empty)";
if (workspace != null)
    name = workspace.getClass().getSimpleName();
log.info("Workspace = {}", name);
```

Another example working with the provided `workspace`:

```java
// Print out all enum names in the current workspace, if one is open.
if (workspace == null) return;
workspace.findClasses(Accessed::hasEnumModifier).stream()
    .map(c -> c.getValue().getName())
    .forEach(name -> log.info("Enum: {}", name));
```

## Using services

Scripts are ran when a user requests them, so you _generally_ do not need to care about whether a service is `@ApplicationScoped` or `@WorkspaceScoped`. The assumption is the user will run the script when it is needed. So a script that uses workspace-scoped content will only be used when a workspace is opened. Of course, if the script is going to load a new workspace, then you will need to follow the same process as described for plugins when using workspace scoped services.

Simple scripts do not use services. Scripts using the full class form will be able to use services.

```java
// ==Metadata==
// @name Content loader
// @description Script to load content from a pre-set path.
// @version 1.0.0
// @author Col-E
// ==/Metadata==

import jakarta.enterprise.context.Dependent;
import jakarta.inject.Inject;
import org.slf4j.Logger;
import software.coley.recaf.analytics.logging.Logging;
import software.coley.recaf.info.JvmClassInfo;
import software.coley.recaf.services.workspace.WorkspaceManager;
import software.coley.recaf.services.workspace.io.ResourceImporter;
import software.coley.recaf.workspace.model.BasicWorkspace;
import software.coley.recaf.workspace.model.Workspace;
import software.coley.recaf.workspace.model.resource.WorkspaceResource;
import java.nio.file.Paths;

@Dependent
public class LoadContentScript {
    private static final Logger logger = Logging.get("load-script");
    private final ResourceImporter importer;
    private final WorkspaceManager workspaceManager;

    // We're injecting the importer to load 'WorkspaceResource' instances from paths on our system
    // then we use the workspace manager to set the current workspace to the loaded content.
    @Inject
    public LoadContentScript(ResourceImporter importer, WorkspaceManager workspaceManager) {
        this.importer = importer;
        this.workspaceManager = workspaceManager;
    }

    // Scripts following the class model must define a 'void run()'
    public void run() {
        String path = "C:/Samples/Test.jar";
        try {
            // Load resource from path, wrap it in a basic workspace
            WorkspaceResource resource = importer.importResource(Paths.get(path));
            Workspace workspace = new BasicWorkspace(resource);

            // Assign the workspace so the UI displays its content
            workspaceManager.setCurrent(workspace);
        } catch (Exception ex) {
            logger.error("Failed to read content from '{}' - {}", path, ex.getMessage());
        }
    }
}
```

<div class="hidden">TODO: Fix link to chapter index page - https://github.com/rust-lang/mdBook/issues/2060</div>

For the list of available services, see [the service lists](../services/index.html).