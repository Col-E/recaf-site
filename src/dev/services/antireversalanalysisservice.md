# AntiReversalAnalysisService

The anti-reversal analysis service manages and executes separate analysis processes that detect anti-reverse-engineering techniques. It ties into the workspace summary's *"Anti-Decompilation"* section shown in the workspace summary panel to offer quick one-click actions that patch detected techniques.

The service allows you to:

* Register and unregister `AntiReversalAnalyzer` instances _(such as the illegal-name and transformer-impact analyzers)_
* Run a specific analyzer against a workspace resource and get back its typed result

## Running an analyzer

```java
// The result type is determined by the analyzer type passed in
IllegalNameAnalysis result = antiReversalAnalysisService.analyze(workspace, resource, IllegalNameAntiReversalAnalyzer.class);
for (ClassPathNode classPath : result.classesWithIllegalNames())
    logger.info("Class with illegal names: {}", classPath.getValue().getName());
```

## Registering your own analyzer

When creating your own analyzer, you'll need to also create an associated result type implementing `AntiReversalAnalysisResult`.

```java
AntiReversalAnalyzer<Foo> fooAnalyzer = new AntiReversalAnalyzer<>() {
    @Nonnull
    @Override
    public Foo analyze(@Nonnull Workspace workspace, @Nonnull WorkspaceResource resource) {
        // TODO: Your analysis logic here
        return new Foo();
    }
    
    @Nonnull
    @Override
    public String getServiceId() {
        return "example-analyzer";
    }
    @Nonnull
    @Override
    public Class<Foo> getResultType() {
        return Foo.class;
    }
};
```
