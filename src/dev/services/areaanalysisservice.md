# AreaAnalysisService

The area analysis service breaks down the classes of a workspace resource into logical *"areas"* based on control flow analysis, showing how the different parts of an application reference one another and generally what sort of purpose they serve.

The service allows you to:

* Analyze a `WorkspaceResource` and get an `AreaAnalysisResult` containing the discovered groups and the links between them.
* Feed the result into the UI's area analysis view _(via `Actions#openAreaAnalysis`)_.


## Analyzing a resource

```java
AreaAnalysisResult result = areaAnalysisService.analyze(workspace, resource);

// Areas (groups) and their purpose
for (AreaGroup group : result.groups())
    logger.info("Area {}: {}", group.id(), group.purpose());

// Links between areas
for (AreaLink link : result.links())
    logger.info("Link: {} -> {} (weight={})", link.sourceGroupId(), link.targetGroupId(), link.weight());
```
