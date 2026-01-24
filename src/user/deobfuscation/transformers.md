# Transformers

Recaf has a tool that matches and *"transforms"* obfuscated code patterns. It can be accessed via `Analysis > Deobfuscation`.

<figure><img src="../../assets/deobfuscate-window.png" alt="deobfuscate-window" /><figcaption><p>A screenshot of the deobfuscation window, annotated with labels to indicate different regions in the UI</p></figcaption></figure>

From the image above, there are three columns:

1. The transformer selection column
2. The transformer order column
3. The preview column

The first column outlines the available transformers you can activate. Clicking the checkbox in the tree will activate it, and it will appear in the second column. The second column allows you to click and drag to move the transformers into different orders. Some transformers work best when they have other transformers run before/after them *(They will reccomend predecessors/successors when you when you activate them)* but getting the order perfect isn't strictly necessary. The last column contains the preview of what your transformers will do.

The preview column has several controls. From the image they are:

- **A**: The *"Pick preview class"* button
  - When clicked, you are prompted to select a class from the workspace.
  - The class you pick will be displayed in the preview space above, initially as decompiled code.
- **B**: The preview mode toggle button
  - When clicked, the output changes from being decompiled code into disassembled bytecode.
- **C**: The transformer max pass count
  - The maximum number of times to run transformers.
  - Some transformers like *constant folding*, when paired with *predicate simplification*, may require more passes to catch all possible patterns that can be optimized.
  - Classes only get processed up to the maximum pass count while transformers observe changes on a class. If the transformers are used on a class, and the class does not change as a result, then it will not be processed any further.
- **D**: The apply button
  - When clicked, the transformers you've selected are applied on every class in the workspace.

As an example, here is a short video detailing how this window can be used on a generic obfuscated input:

<video src="../../assets/deobfuscate-opaques.mp4" controls></video>

## Built-in transformers

- **Call result inlining**: Replaces the calls to static methods with the result of the call if the target method can be emulated and all parameter values are known. For instance: `Math.max(5, 10)` will become `10` and `rot13("uryyb")` will become `"hello"` if the implementation of `rot13` is accessible in the workspace and uses API's supported by emulation.
  - Some obfuscators with weak String encryption capabilities can be defeated with this transformer without any additional effort. However, there are some obfuscators with String encryption routines that are *almost* supported, but with a little bit of manual work on your part can become emulated. 
  - For information on which core Java API's support emulation refer to the `Lookup` implementation classes in the [analysis lookup package](https://github.com/Col-E/Recaf/tree/master/recaf-core/src/main/java/software/coley/recaf/util/analysis/lookup).
  - For information on which workspace-defined methods support emulation, refer to the `canEvaluate` methods defined in [`ReEvaluator`](https://github.com/Col-E/Recaf/blob/master/recaf-core/src/main/java/software/coley/recaf/util/analysis/ReEvaluator.java).
- **Cycle class removal**: Removes junk classes from the workspace that have cyclic inheritance. For instance: `A extends B` and `B extends A`
- **Dead code removal**: Removes code in control flow paths that cannot ever be taken. For instance the contents of the following block: `if (1 > 2) { ... }`
- **Duplicate annotation removal**: Removes duplicate annotation declarations on classes, fields, and methods. This is illegal in Java source code, but valid in Java bytecode.
- **Duplicate catch merging**: Combines multiple consecutive `catch` handler blocks with duplicate code into a single block.
- **Enum name restoration**: Detects and renames enum keys that are leaked as String constants in the static initializer of `enum` classes.
  - When String encryption is present, that will have to be cleaned up before this transformer can be used.
- **Stack frame removal**: Strips all stack frames from methods which will trigger regeneration upon the transformation being completed. Some obfuscators can create junk frames which confuse or break other tools and this allows quick patching for that.
- **Goto inlining**: Where possible, inlines the target control flow block of `goto` instructions at the calling location.
- **Illegal annotation removal**: Removes malformed annotations that are created by obfuscators to crash decompilers and other RE tools.
- **Illegal name mapping**: Renames any class, field, or method which has names not compatible if used in Java source code. For instance, whitespaces, reserved keywords, etc. Essentially a one-click version of the [mapping generator](mapping.md).
- **Illegal signature removal**: Removes malformed generic type signatures that are created by obfuscators to crash decompilers and other RE tools.
- **Illegal varargs removal**: Removes the `varargs` modifier from methods where it is improperly used. This is used by obfuscators to crash some decompilers.
- **Kotlin name restoration**: Detects and renames classes, fields, and methods with information found in `@Metadata` and `@JvmName` annotations.
  - The `@Metadata` data is stored in a protobuf format which does not appear to keep the same declaration order as fields and methods are defined by in the class. This limits which fields and methods can be renamed since we have to prove that only one possible field or method can be mapped to an entry in the protobuf model. If you have three fields of the same type such as an `int` then we cannot disambiguate which `int` maps to which entry in the model. But if you had a `int`, `float`, and `double` the types are unique and all three could be remapped.
- **Long annotation removal**: Removes bogus long annotations. Usually obfuscators will combine stupidly long names with duplicate annotation entries *(see above)* to slow down decompilers.
- **Long exception removal**: Removes bogus long types on method `throws` declarations. Same idea as long annotations, usually intended to slow down decompilers.
-  **Opaque constant folding**: Folds constant operations into single values. For instance: `1 + 1` becomes `2`, `x + 0` becomes `x`.
- **Opaque predicate simplification**: Replaces control flow predicates that can be proven to always take a specific path with a single `goto`. Generally should be paired with the goto inlining transformer.
- **Redundant try-catch removal**: Removes `catch` blocks that can be proven to not throw a given exception type.
- **Source name restoration**: Detects and renames classes which still have a seemingly valid `SourceFile` attribute. Has some basic checks to prevent renaming to bogus, but you should generally check a few classes with the assembler or low level view to verify the classes have valid attribute values.
- **Static value inlining**: Emulates static initializers of classes in the workspace to and replaces any references to discovered constant fields with inline constants.
- **Unknown attribute removal**: Removes any attribute on classes, fields, or methods that is not recognized in the JVM specification.
- **Variable folding**: Replaces redundant usage of variables with constants, or simpler variable expressions. For instance: `a = 10, b = a, c = b, foo(c)` becomes `foo(10)` 
- **Variable table normalization**: Recreates method local variable tables with a basic pattern of incrementing names for parameters and scoped local variables. Can be useful when methods are filled with junk variable entries to clean up decompiled and disassembled output.

## Custom transformers

Plugins can register their own transformers with the [`TransformationManager`](../../dev/services/transformationmanager.md).
