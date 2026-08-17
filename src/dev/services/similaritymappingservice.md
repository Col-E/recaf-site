# SimilarityMappingService

The similarity mapping service generates mappings between the classes of two workspace resources based on structural similarity. This is useful when you have two versions of an application, one unobfuscated and one obfuscated, and want to map the obfuscated names back to the clear ones.

The service allows you to:

* Analyze a source and target resource, producing a `SimilarityMappingsReport` with preview metadata.
* Generate `Mappings` directly from the same analysis.

See the section on [Similarity mapping under 'Mapping'](../../user/deobfuscation/mapping.md) for the user-facing feature.

## Options

`SimilarityMappingOptions` controls the matching behavior:

* `classSimilarityThresholdPercent`: Minimum structural similarity for a class match.
* `classCertaintyGapPercent`: Minimum top-vs-runner-up similarity gap for a class match.
* `memberSimilarityThresholdPercent`: Minimum similarity for field/method matches within a matched class.
* `maxFullScoreCandidates`: Maximum candidates retained for scoring after pre-screening.
* `shortlistGapThresholdPercent`: Score gap threshold to drop much-worse candidates early.

## Generating mappings

```java
SimilarityMappingOptions options = new SimilarityMappingOptions(
        /* classSimilarityThresholdPercent */ 95,
        /* classCertaintyGapPercent */         1,
        /* memberSimilarityThresholdPercent */ 95,
        /* maxFullScoreCandidates */           25,
        /* shortlistGapThresholdPercent */     10);

// Generate mappings directly
Mappings mappings = similarityMappingService.generate(workspace, sourceResource, targetResource, options);

// Or analyze for a preview, then apply via the mapping applier
SimilarityMappingsReport report = similarityMappingService.analyze(workspace, sourceResource, targetResource, options);
```

For applying, see [MappingApplierService](mappingapplierservice.md).
