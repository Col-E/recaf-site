# Services

As Recaf is driven by CDI, almost all of its features are defined as `@Inject`-able service classes.

## API

These are the services defined in the `core` module.

* [AggregateMappingManager](aggregatemappingmanager.md)
* [AndroidAnalysisService](androidanalysisservice.md)
* [AntiReversalAnalysisService](antireversalanalysisservice.md)
* [AreaAnalysisService](areaanalysisservice.md)
* [AssemblerPipelineManager](assemblerpipelinemanager.md)
* [AstService](astservice.md)
* [AttachManager](attachmanager.md)
* [CallGraphService](callgraphservice.md)
* [CommentManager](commentmanager.md)
* [ConfigManager](configmanager.md)
* [DecompilerManager](decompilemanager.md)
* [EntryAnalysisService](entryanalysisservice.md)
* [FileMetadataAnalysisService](filemetadataanalysisservice.md)
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
* [PluginManager](pluginmanager.md)
* [ResourceImporter](resourceimporter.md)
* [ScriptEngine](scriptengine.md)
* [ScriptManager](scriptmanager.md)
* [SearchService](searchservice.md)
* [SimilarityMappingService](similaritymappingservice.md)
* [SimilaritySearchService](similaritysearchservice.md)
* [SnippetManager](snippetmanager.md)
* [TransformationApplierService](transformationapplierservice.md)
* [TransformationManager](transformationmanager.md)
* [WorkspaceManager](workspacemanager.md)
* [WorkspaceProcessingService](workspaceprocessingservice.md)

## UI

The `ui` module defines a number of new service types dedicated to UI behavior.

* [Actions](actions.md)
* [CellConfigurationService](cellconfigurationservice.md) _(Wraps these services)_
  * [ContextMenuProviderService](contextmenuproviderservice.md)
  * [IconProviderService](iconproviderservice.md)
  * [TextProviderService](textproviderservice.md)
* [ConfigComponentManager](configcomponentmanager.md)
* [ConfigIconManager](configiconmanager.md)
* [DockingManager](dockingmanager.md)
* [FileTypeSyntaxAssociationService](filetypesyntaxassociationservice.md)
* [NavigationManager](navigationmanager.md)
* [PathExportingManager](pathexportingmanager.md)
* [PathLoadingManager](pathloadingmanager.md)
* [ResourceSummaryService](resourcesummaryservice.md)
* [WindowFactory](windowfactory.md)
* [WindowManager](windowmanager.md)
* [WindowStyling](windowstyling.md)
