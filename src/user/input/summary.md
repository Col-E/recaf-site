# Summarization

After you load a workspace, Recaf will do some basic analysis of its contents and provide you with a summary of what was found. The kind of information supported includes:

- The number of classes and files
- The required permissions for Android applications
- The entry points
  - For regular jar files, `public static void main(String[] args) { ... }`
  - For Android applications, listed `Activity` subclasses defined in the application manifest

- Hash information of the primary resource
  - The right side buttons copy the hash value to your clipboard. This can be useful if you want to look up files on services like VirusTotal or AnyRun quickly.

<figure><img src="../../assets/workspace-summary.png" alt="Summary display" /><figcaption><p>Different kinds of files may have different summary content. In this example we have a side-by-side of a regular Java application and an Android application.</p></figcaption></figure>