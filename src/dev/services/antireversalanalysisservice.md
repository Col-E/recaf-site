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

## Adding UI for your analyzer

To present your analyzer's results in the workspace summary, implement an `AntiReversalResultPresenter`. The presenter is matched to an analyzer by its service ID, so `getAnalyzerId()` must return the same value as the analyzer's `getServiceId()`.

```java
public class FooResultPresenter implements AntiReversalResultPresenter {
    @Nonnull
    @Override
    public String getAnalyzerId() {
        return "example-analyzer"; // Must match the analyzer's 'getServiceId()'
    }

    @Nonnull
    @Override
    public Class<? extends AntiReversalAnalysisResult> getResultType() {
        return Foo.class;
    }

    @Override
    public int getPriority() {
        // Lower values run first, higher values run later.
        return 0;
    }

    @Override
    public boolean isApplicable(@Nonnull AntiReversalAnalysisResult result) {
        // Runs before JavaFX work is scheduled.
        // Used to determine if a summary needs to be appended based on the results.
        return result instanceof Foo foo && foo.hasFindings();
    }

    @Override
    public void appendSummary(@Nonnull Workspace workspace,
                              @Nonnull WorkspaceResource resource,
                              @Nonnull AntiReversalAnalysisResult result,
                              @Nonnull SummaryConsumer consumer,
                              @Nonnull Executor actionExecutor) {
        Foo foo = (Foo) result;

        Label label = new Label(foo.getFindingCount() + " finding(s)");
        Button fix = new Button("Fix");
        fix.setOnAction(e -> actionExecutor.execute(() -> {
            // Run your patch action asynchronously.
        }));

        // Append one summary row with the standard layout.
        consumer.appendSummary(PresenterUtils.box(fix, label));
    }
}
```

You then can register it via `AntiReversalResultPresenterService`:

```java
antiReversalResultPresenterService.registerPresenter(presenter);
antiReversalResultPresenterService.removePresenter(presenter);
```
