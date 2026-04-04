# Inputs

As a Java bytecode editor Recaf supports loading a variety of file formats seen in the Java ecosystem. Supported formats include:

- `.class`
- `.jar` / `.zip` / `.war` / `.aar` *(See: [Zip Obfuscation](../obfuscation/zip.md))*
- `.jmod`
- `.apk` / `.dex`
- `modules` _(Read-only)_

Files are not restricted by their extension. So long as the contents of a file are one of the supported formats the name doesn't matter. Recaf will auto-detect the content type. The following pages will have additional information about loading inputs.