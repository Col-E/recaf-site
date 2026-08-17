# FileMetadataAnalysisService

The file metadata analysis service computes metadata about the primary resource of a workspace, which is shown in the workspace summary. It supports:

* Computing hash values of the primary file and its direct embedded file resources
* Analyzing the signing contents of JAR files _(signature block files and certificates)_

## Computing hashes

```java
List<FileHashResult> hashes = fileMetadataAnalysisService.computeHashes(workspace, resource);
for (FileHashResult hash : hashes)
    for (Map.Entry<HashAlgorithm, String> entry : hash.hashes().entrySet())
        logger.info("{}: {}", entry.getKey(), entry.getValue());
```

## Analyzing jar signing

```java
// Returns 'null' when the resource is not a signed jar file
JarSigningReport report = fileMetadataAnalysisService.analyzeJarSigning(workspace, resource);
if (report != null) {
    logger.info("Signed jar with {} signature file(s)", report.signatureFilePaths().size());
    for (JarCertificateResult cert : report.certificateResults())
        logger.info("Certificates in {}: {}", cert.filePath(), cert.certificates().size());
}
```
