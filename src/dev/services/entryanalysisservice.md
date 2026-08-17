# EntryAnalysisService

The entry analysis service discovers the entry points of an application, which are shown in the workspace summary. It supports:

* Registering and unregistering `EntryPointDiscovery` implementations
* Discovering entry points for a workspace resource

The built-in discovery implementations cover:

* `JvmMainEntryPointDiscovery`: `public static void main(String[])` methods.
* `AndroidActivityEntryPointDiscovery`: `Activity` subclasses from the manifest.
* `BukkitPluginEntryPointDiscovery`: `JavaPlugin` implementations.
* `FabricModEntryPointDiscovery`: `ModInitializer` implementations.
* `ForgeModEntryPointDiscovery`: Forge/NeoForge mod initializer implementations.
* `VelocityPluginEntryPointDiscovery`: Velocity plugin main classes.

## Finding entry points

```java
List<EntryPoint> entryPoints = entryAnalysisService.findEntryPoints(workspace, resource);
for (EntryPoint entryPoint : entryPoints)
    logger.info("{} -> {}", entryPoint.kind().displayName(), entryPoint.targetPath());
```
