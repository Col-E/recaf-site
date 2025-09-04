# Class Parsing Library Exploitation

How do you prevent a reverse engineer from looking at your class file? By making a really janky class that violates the specification and/or abuses very niche but supported cases. Most Java class file parsers take the specification at face value and assume that inputs are generally _"normal"_ classes that have been freshly compiled by `javac`. For instance you probably expect the `Code` attribute to only appear on methods, right? Well, there's nothing stopping you from putting a `Code` attribute on a field. And when its on a field, the JVM at runtime will ignore it. But a Java class parser will be expected to parse the class in full, so it will try to read the `Code` attribute on the field. If this `Code` attribute has bogus values in it, the class parser can easily be tricked into reading outside of the class file bounds, or casting to an illegal type.

## What does Recaf do?

Recaf automatically patches these classes when loading classes into the workspace. We wrote [CafeDude](https://github.com/Col-E/CAFED00D/) _(CAFED00D)_ to read janky classes like this. Its not intended to be used for high level operations like [ASM](https://asm.ow2.io/) or [Javassist](https://www.javassist.org/) are. Instead, its primary purpose is transforming these janky classes into safe classes that can be read by the other high level Java class parsing libraries. 

If CafeDude is incapable of patching a class file, Recaf will include the file under the *"Files"* tree in the workspace explorer. Sometimes this occurs because a file may look like a class at first glance, but is not actually valid. Please [report any cases](https://github.com/Col-E/CAFED00D/issues) that you believe should be patchable where CafeDude fails and we will take a look.

