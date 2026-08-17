# WindowStyling

The `WindowStyling` service manages the CSS stylesheets applied to Recaf's windows.

The service allows you to:

* Add stylesheets from file paths or URLs.
* Get the list of currently applied stylesheet URIs.

## Adding a stylesheet

```java
// From a path on disk.
windowStyling.addStylesheet(Paths.get("./custom-theme.css"));

// Or from a URL (will get cached on-disk in %TMP%)
URL url = new URL("https://example.com/custom-theme.css"); 
//  url = MyClass.class.getResource("./custom-theme.css")
boolean added = windowStyling.addStylesheet(url);

// Inspect current stylesheets.
// These will get applied to any window registered with the window manager.
List<String> stylesheets = windowStyling.getStylesheetUris();
```
