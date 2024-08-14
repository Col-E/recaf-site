# Faster builds in IntelliJ

Open your IntelliJ settings once the project is open and navigate to `Build, Execution, Deployment | Build Tools | Gradle`. Change the _"using"_ options to IDEA instead of Gradle.

<figure><img src="../../assets/IntelliJ-gradle-compile.png" alt=""><figcaption><p>Changing the Gradle settings to build using IDEA instead of Gradle speeds things up a lot.</p></figcaption></figure>

You will need to do `gradlew build` at least once before doing this to create a few files created by the build script.