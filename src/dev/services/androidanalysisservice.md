# AndroidAnalysisService

The Android analysis service provides Android-specific analysis over a workspace:

* Find the permissions requested by an Android application
* Get detailed information about each requested permission _(name, protection level, description)_

## Finding requested permissions

```java
// Requires the workspace to contain an Android resource
Workspace workspace = ...;

// Basic set of requested permissions
List<AndroidPermissionEntry> permissions = androidAnalysisService.findRequestedPermissions(workspace);
for (AndroidPermissionEntry permission : permissions)
    logger.info("Permission '{}' declared by '{}'", permission.permission(), permission.elementName());

// Detailed information for each requested permission
List<AndroidPermissionDetails> details = androidAnalysisService.findRequestedPermissionDetails(workspace);
for (AndroidPermissionDetails detail : details)
    logger.info("{}: levels={}", detail.entry().permission(), detail.levels());
```
