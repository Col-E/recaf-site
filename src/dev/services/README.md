# Services

As Recaf is driven by CDI, almost all of its features are defined as `@Inject`-able service classes.

## API

These are the services defined in the `core` module.

* [AggregateMappingManager](aggregatemappingmanager.md)
* AstService
* AssemblerPipelineManager
* [AttachManager](attachmanager.md)
* [CallGraphService](callgraphservice.md)
* [CommentManager](commentmanager.md)
* [ConfigManager](configmanager.md)
* [DecompileManager](decompilemanager.md)
* [GsonProvider](gsonprovider.md)
* [InfoImporter](infoimporter.md)
* [InheritanceGraphService](inheritancegraphservice.md)
* [JavacCompiler](javaccompiler.md)
* [MappingApplierService](mappingapplierservice.md)
* [MappingFormatManager](mappingformatmanager.md)
* [MappingGenerator](mappinggenerator.md)
* [MappingListeners](mappinglisteners.md)
* [NameGeneratorProviders](namegeneratorproviders.md)
* [PatchApplier](patchapplier.md)
* [PatchProvider](patchprovider.md)
* [PhantomGenerator](phantomgenerator.md)
* PluginManager
* [ResourceImporter](resourceimporter.md)
* [ScriptEngine](scriptengine.md)
* [ScriptManager](scriptmanager.md)
* [SearchService](searchservice.md)
* [SnippetManager](snippetmanager.md)
* [TransformationApplierService](transformationapplierservice.md)
* [TransformationManager](transformationmanager.md)
* [WorkspaceManager](workspacemanager.md)
* [WorkspaceProcessingService](workspaceprocessingservice.md)

## UI

The `ui` module defines a number of new service types dedicated to UI behavior.

* Actions
* CellConfigurationService _(Wraps these services)_
  * ContextMenuProviderService
  * IconProviderService
  * TextProviderService
* ConfigComponentManager
* ConfigIconManager
* FileTypeSyntaxAssociationService
* NavigationManager
* [PathExportingManager](pathexportingmanager.md)
* [PathLoadingManager](pathloadingmanager.md)
* [ResourceSummaryService](resourcesummaryservice.md)
* WindowFactory
* WindowManager
* WindowStyling