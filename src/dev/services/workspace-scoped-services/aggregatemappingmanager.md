# AggregateMappingManager

The aggregate mapping manager maintains an `AggregatedMappings` instance *(which extends `IntermediateMappings`)* representing the sum of all mapping operations applied to the current workspace.

## Getting the aggregate mappings

To do feature A you do XYZ, here is a sample.

```java
@Inject AggregateMappingManager aggManager;

// Get the mappings on-demand
AggregatedMappings mappings = aggManager.getAggregatedMappings();

// Register a listener to be notified of changes
aggManager.addAggregatedMappingsListener(mappings -> {
    // Called when any workspace mapping are applied
});
```
