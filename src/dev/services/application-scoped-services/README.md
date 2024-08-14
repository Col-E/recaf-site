# Application scoped services

These services are available for injection at any point.

## API

These are the services defined in the `core` module.

* [AttachManager](attachmanager.md)
* [CommentManager](commentmanager.md)
* [ConfigManager](configmanager.md)
* [DecompileManager](decompilemanager.md)
* [GsonProvider](gsonprovider.md)
* [InfoImporter](infoimporter.md)
* [JavacCompiler](javaccompiler.md)
* [MappingFormatManager](mappingformatmanager.md)
* [MappingGenerator](mappinggenerator.md)
* [MappingListeners](mappinglisteners.md)
* NameGeneratorProviders
* PatchApplier
* PatchProvider
* [PhantomGenerator](phantomgenerator.md)
* PluginManager
* [ResourceImporter](resourceimporter.md)
* [ScriptEngine](scriptengine.md)
* [ScriptManager](scriptmanager.md)
* [SearchService](searchservice.md)
* [SnippetManager](snippetmanager.md)
* [WorkspaceManager](workspacemanager.md)

## UI

The `ui` module defines a number of new service types dedicated to UI behavior.

* Actions
* CellConfigurationService _(Wraps these services)_
  * ContextMenuProviderService
  * IconProviderService
  * TextProviderService
* ConfigComponentManager
* ConfigIconManager
* FileTypeAssociationService
* NavigationManager
* PathExportingManager
* [PathLoadingManager](pathloadingmanager.md)
* [ResourceSummaryService](resourcesummaryservice.md)
* WindowFactory
* WindowManager