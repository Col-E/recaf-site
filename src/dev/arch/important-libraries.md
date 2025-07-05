# Important libraries

A brief overview of the major dependencies Recaf uses in each module.

## Core

**JVM Bytecode Manipulation**: Recaf uses [ASM](https://asm.ow2.io/) and [CafeDude](https://github.com/Col-E/CAFED00D) to parse bytecode. Most operations will be based on ASM since heavily abstracts away the class file format, making what would otherwise be tedious work simple. CafeDude is used for lower level operations and patching classes that are not compliant with ASM.

**Android to Java Conversion**: Recaf uses [dex-translator](https://github.com/Col-E/dex-translator/) to map the Dalvik bytecode of classes into JVM bytecode. This process is a bit lossy, but allows the use of JVM tooling _(like the different decompilers)_ on Android content.

**Android Dalvik Bytecode Manipulation**: We are currently investigating on how to handle Dalvik manipulation.

**ZIP Files**: Recaf uses [LL-Java-Zip](https://github.com/Col-E/LL-Java-Zip) to read ZIP files. The behavior of LL-Java-Zip is configurable and can mirror interpreting archives in different ways. This is important for Java reverse engineering since the JVM itself has some odd parsing quirks that most other libraries do not mirror. More information about this can be read on the LL-Java-Zip project page.

**Source Parsing**: Recaf uses [SourceSolver](https://github.com/Col-E/SourceSolver) to parse Java source code. The reasons for using our own solution over other more mainstream parsing libraries are that:

1. The AST model is error resilient. This is important since code Recaf is decompiling may not always yield perfectly correct Java code, especially with more intense forms of obfuscation. The ability to ignore invalid sections of the source while maintaining the ability to interact with recognizable portions is very valuable.
2. The AST model parses very fast and context resolution is equally as fast. We do not want to use solutions that create noticeable lag in Recaf's user interface.
3. We own the project, so we can fix bugs and create new releases whenever we want. Other parser projects vary in the rate of their release cycles.

**CDI**: Recaf uses [Weld](https://weld.cdi-spec.org/) as its CDI implementation. You can read the [CDI](cdi.md) article for more information.

## UI

**JavaFX**: Recaf uses [JavaFX](https://openjfx.io/) as its UI framework. The observable property model it uses makes managing live updates to UI components when backing data is changed easy. Additionally, it is styled via CSS which makes customizing the UI for Recaf-specific operations much more simple as opposed to something like Swing.

**AtlantaFX**: Recaf uses [AtlantaFX](https://github.com/mkpaz/atlantafx) to handle common theming.

**Ikonli**: Recaf uses [Ikonli](https://github.com/kordamp/ikonli) for scalable icons. Specifically the [Carbon](https://kordamp.org/ikonli/cheat-sheet-carbonicons.html) pack.

**Docking**: Recaf uses [BentoFX](https://github.com/Col-E/BentoFX) for handling dockable containers/tabs across all open windows.
