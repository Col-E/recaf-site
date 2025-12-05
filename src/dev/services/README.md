# Services

As Recaf is driven by CDI, almost all of its features are defined as `@Inject`-able service classes.

## API

These are the services defined in the `core` module.

* [AggregateMappingManager](aggregatemappingmanager.md)
* [AstService](astservice.md)
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
  * [ContextMenuProviderService](contextmenuproviderservice.md)
  * [IconProviderService](iconproviderservice.md)
  * [TextProviderService](textproviderservice.md)
* ConfigComponentManager
* ConfigIconManager
* [DockingManager](dockingmanager.md)
* FileTypeSyntaxAssociationService
* [NavigationManager](navigationmanager.md)
* [PathExportingManager](pathexportingmanager.md)
* [PathLoadingManager](pathloadingmanager.md)
* [ResourceSummaryService](resourcesummaryservice.md)
* WindowFactory
* WindowManager
* WindowStyling