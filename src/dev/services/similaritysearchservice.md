# SimilaritySearchService

The similarity search service finds classes and methods in the workspace that are similar to a given reference, based on fingerprinting of their structure. This is useful for finding the same logic across multiple resources _(for example, the same class in different versions of an application, or similar decryption functions from an obfuscator)_.

The service allows you to:

* Search for methods similar to a reference method
* Search for classes similar to a reference class

## Searching for similar methods

```java
List<SimilarMethodSearchResult> results = similaritySearchService.searchMethods(referenceMethodPath, similarMethodSearchOptions);
for (SimilarMethodSearchResult result : results)
    logger.info("Similar method: {} ({}%)", result.path(), result.similarity());
```

## Searching for similar classes

```java
List<SimilarClassSearchResult> results = similaritySearchService.searchClasses(referenceClassPath, similarClassSearchOptions);
for (SimilarClassSearchResult result : results)
    logger.info("Similar class: {} ({}%)", result.path(), result.similarity());
```
