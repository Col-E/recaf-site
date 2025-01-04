# PatchApplier

The patch applier applies `WorkspacePatch` values to a given `Workspace`.

## Generating patches

See [PatchProvider](patchprovider.md):

- For auto-creating patches based on changes made in an existing `Workspace`
- For loading patches from JSON

You could also manually construct the `WorkspacePatch` instance yourself.

## Applying patches

```java
// Optional feedback interface implementation for receiving details about patch failures.
// Can be 'null' to ignore feedback.
PatchFeedback feedback = new PatchFeedback() {
    @Override
    public void onAssemblerErrorsObserved(@Nonnull List<Error> errors) {
        // assembler patch has failed, patch process abandoned
    }
    @Override
    public void onIncompletePathObserved(@Nonnull PathNode<?> path) {
        // patch had path that was invalid, patch process abandoned
    }
};

// If the patch was applied, we return 'true'
// If errors were seen, the patch is abandoned and we return 'false'
boolean success = patchApplier.apply(patch, feedback);
```