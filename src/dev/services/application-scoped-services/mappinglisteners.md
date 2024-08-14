# MappingListeners

The mapping listeners service allows you to listen to when mappings are applied to any `Workspace`.

## Listening to mapping operations

Just call `addMappingApplicationListener(MappingApplicationListener)` with your listener implementation.

Here is an example implementation with some comments explaining the contents of the mapping results model:

```java
class ExampleMappingListener implements MappingApplicationListener {
    @Override
    public void onPreApply(@Nonnull MappingResults mappingResults) {
        // The mappings that were used to create the results
        Mappings mappings = mappingResults.getMappings();
        
        // All names of classes *affected* by mappings can be iterated over.
        //
        // If a class was not renamed, but had contents inside it that point to renamed content
        // it will be included in this map and the key/value will be equal.
        // Otherwise, the post-map-name will be the class's renamed name.
        mappingResults.getMappedClasses().forEach((preMapName, postMapName) -> {
            ClassPathNode preMappingPath = mappingResults.getPreMappingPath(preMapName);
            ClassPathNode postMappingPath = mappingResults.getPostMappingPath(postMapName);
            
            // The 'results' model already has the contents of classes after mapping is applied.
            // They just have not been copied back into the workspace yet.
            ClassInfo preMappedClass = preMappingPath.getValue();
            ClassInfo postMappedClass = postMappingPath.getValue();
        });
    }
    
    @Override
    public void onPostApply(@Nonnull MappingResults mappingResults) {
        // The results model is the same as the 'onPreApply' but the workspace has now been
        // updated to replace old classes with the updated instances.
    }
}
```