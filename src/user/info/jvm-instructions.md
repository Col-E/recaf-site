# JVM Bytecode Instructions

##  Preface

This is a listing of the [Java bytecode instructions](https://en.wikipedia.org/wiki/Java_bytecode_instruction_listings) grouped roughly into use cases. For instance, `ifeq` and `goto` belong to the `Control Flow` group.

What differentiates this list from the [official instruction specification](https://docs.oracle.com/javase/specs/jvms/se25/html/jvms-6.html) is that the descriptions have been modified to fit how Recaf represents them in the assembler.

## Table of Contents

- [Constants](#constants)
- [Object Creation](#object-creation)
- [Arrays](#arrays)
- [Variables](#variables)
- [Stack Math](#stack-math)
- [Stack Manipulation](#stack-manipulation)
- [Control Flow](#control-flow)
- [Fields](#fields)
- [Method Calls](#method-calls)
- [Dynamic Method Calls](#dynamic-methods-calls)
- [Type Conversion](#type-conversion)
- [Returns](#returns)
- [Miscellaneous](#miscellaneous)

## Constants

| Opcode         | Stack: [before]→[after]  | Description                              |
|----------------|--------------------------|------------------------------------------|
| `aconst_null`  | → `null`                 | push a `null` reference onto the stack   |
| `dconst_0`     | → `0.0`                  | push the constant `0.0` onto the stack   |
| `dconst_1`     | → `1.0`                  | push the constant `1.0` onto the stack   |
| `fconst_0`     | → `0.0f`                 | push `0.0f` on the stack                 |
| `fconst_1`     | → `1.0f`                 | push `1.0f` on the stack                 |
| `fconst_2`     | → `2.0f`                 | push `2.0f` on the stack                 |
| `iconst_m1`    | → `-1`                   | load the `int` value `−1` onto the stack |
| `iconst_0`     | → `0`                    | load the `int` value `0` onto the stack  |
| `iconst_1`     | → `1`                    | load the `int` value `1` onto the stack  |
| `iconst_2`     | → `2`                    | load the `int` value `2` onto the stack  |
| `iconst_3`     | → `3`                    | load the `int` value `3` onto the stack  |
| `iconst_4`     | → `4`                    | load the `int` value `4` onto the stack  |
| `iconst_5`     | → `5`                    | load the `int` value `5` onto the stack  |
| `lconst_0`     | → `0L`                   | push `long` `0L` onto the stack          |
| `lconst_1`     | → `1L`                   | push `long` `1L` onto the stack          |
| `ldc` <ul><li>value</li></ul> | → value   | push a constant value _(`String`, `int`, `float`, `long`, `double`, `Class`, `MethodType`, `MethodHandle`, or `ConstantDynamic`)_ onto the stack |
| `bipush` <ul><li>value</li></ul>  | → value | push a `byte` _(`-128` through `127`)_ onto the stack as an `int` value      |
| `sipush` <ul><li>value</li></ul>  | → value | push a `short` _(`-32768` through `32767`)_ onto the stack as an `int` value |

## Object Creation

| Opcode         | Stack: [before]→[after]  | Description                              |
|----------------|--------------------------|------------------------------------------|
| `new` <ul><li>type</li></ul>  | → objectref   | create new object of `type` |

> **NOTE**: After creation of a new object, its constructor must properly be invoked to initialize the object. You will generally see the pattern:
> ```java
> // Given the code:
> //   String myString = new String(byteArrayHere);
> new java/lang/String
> dup
> aload byteArrayHere
> invokespecial java/lang/String.<init> ([B)V
> astore myString
> ```
> 1. The `new` creates the initial instance.
> 2. The `dup` duplicates the reference to the instance on the stack.
> 3. The `byte[]` parameter to the `String(byte[])` constructor is loaded on the stack.
> 4. The `String(byte[])` constructor is invoked, consuming the `byte[]` paramter and the _duplicated_ `String` reference off of the stack.
> 5. The originally pushed `String` reference is stored in a variable.
>    - Since the items from `dup` are the same instance this stores the `String` value after its constructor is called and initialization occurrs.

## Arrays

| Opcode         | Stack: [before]→[after]    | Description                                |
|----------------|----------------------------|--------------------------------------------|
| `anewarray` <ul><li>type</li></ul>  | count → arrayref        | create a new array of _references_ of length `count` and component type identified by `type` _(A full class name such as `java/lang/String`)_ |
| `newarray` <ul><li>type</li></ul>   | count → arrayref        | create a new array of _primitives_ of length `count` and component type identified by `type` <table ><tr><th>Type</th> <th>Descriptor alias</th></tr><tr><td>boolean</td><td>Z</td></tr><tr><td>char</td><td>C</td></tr><tr><td>float</td><td>F</td></tr><tr><td>double</td><td>D</td></tr><tr><td>byte</td><td>B</td></tr><tr><td>short</td><td>S</td></tr><tr><td>int</td><td>I</td></tr><tr><td>long</td><td>J</td></tr></table> |
| `multianewarray` <ul><li>desc</li><li>dims</li></ul> | count1, [count2,...] → arrayref | create a new array of `dims` dimensions of type identified by `desc` |
| `arraylength`  | arrayref → length          | get the length of an array                 |
| `aaload`       | arrayref, index → value    | load a reference from an array             |
| `aastore`      | arrayref, index, value →   | store a reference into an array            |
| `baload`       | arrayref, index → value    | load a `byte` or `boolean` from an array   |
| `bastore`      | arrayref, index, value →   | store a `byte` or `boolean` into an array  |
| `caload`       | arrayref, index → value    | load a `char` from an array                |
| `castore`      | arrayref, index, value →   | store a `char` into an array               |
| `daload`       | arrayref, index → value    | load a `double` from an array              |
| `dastore`      | arrayref, index, value →   | store a `double` into an array             |
| `faload`       | arrayref, index → value    | load a `float` from an array               |
| `fastore`      | arrayref, index, value →   | store a `float` in an array                |
| `iaload`       | arrayref, index → value    | load an `int` from an array                |
| `iastore`      | arrayref, index, value →   | store an `int` into an array               |
| `laload`       | arrayref, index → value    | load a `long` from an array                |
| `lastore`      | arrayref, index, value →   | store a `long` to an array                 |
| `saload`       | arrayref, index → value    | load `short` from an array                 |
| `sastore`      | arrayref, index, value →   | store `short` to array                     |

## Variables

| Opcode       | Stack: [before]→[after] | Description                                                 |
|--------------|-------------------------|-------------------------------------------------------------|
| `iload`  <ul><li>var</li></ul> | → value     | load an `int` value from a local variable `var`              |
| `lload`  <ul><li>var</li></ul> | → value     | load a `long` value from a local variable `var`              |
| `fload`  <ul><li>var</li></ul> | → value     | load a `float` value from a local variable `var`             |
| `dload`  <ul><li>var</li></ul> | → value     | load a `double` value from a local variable `var`            |
| `aload`  <ul><li>var</li></ul> | → objectref | load a reference onto the stack from a local variable `var`  |
| `istore` <ul><li>var</li></ul> | value →     | store `int` value into variable `var`                        |
| `lstore` <ul><li>var</li></ul> | value →     | store a `long` value in a local variable `var`               |
| `fstore` <ul><li>var</li></ul> | value →     | store a `float` value into a local variable `var`            |
| `dstore` <ul><li>var</li></ul> | value →     | store a `double` value into a local variable `var`           |
| `astore` <ul><li>var</li></ul> | objectref → | store a reference into a local variable `var`                |
| `iinc` <ul><li>var</li><li>amount</li></ul> | [No change]             | increment local variable `var` by a given `amount` _(`byte`)_ |

> **NOTE**: Variables are accessed by index in the class file specification, but Recaf's assembler maps variables to their name as found in the [`LocalVariableTable`](https://docs.oracle.com/javase/specs/jvms/se25/html/jvms-4.html#jvms-4.7.13) _(LVT)_ attribute. 
> If a method does not have a LVT, or if the LVT contains bogus names that are not valid for use within the assembler, then auto-generated names will be used following the pattern `v1`, `v2` ... `vN`.

## Stack Math

| Opcode         | Stack: [before]→[after]  | Description                              |
|----------------|--------------------------|------------------------------------------|
| `dadd`         | value1, value2 → result  | add two `double` values `value2 + value1`                                                                  |
| `ddiv`         | value1, value2 → result  | divide two `double` values `value2 / value1`                                                               |
| `dmul`         | value1, value2 → result  | multiply two `double` values `value2 * value1`                                                             |
| `drem`         | value1, value2 → result  | get the remainder from a division between two `double` values `(value2 - ((value1 / value2) * value2))`    |
| `dsub`         | value1, value2 → result  | subtract a `double` from another `value2 - value1`                                                         |
| `fadd`         | value1, value2 → result  | add two `float` values `value2 + value1`                                                                   |
| `fdiv`         | value1, value2 → result  | divide two `float` values `value2 / value1`                                                                |
| `fmul`         | value1, value2 → result  | multiply two `float` values `value2 * value1`                                                              |
| `frem`         | value1, value2 → result  | get the remainder from a division between two `float` values `(value2 - ((value1 / value2) * value2))`     |
| `fsub`         | value1, value2 → result  | subtract two `float` values `value2 - value1`                                                              |
| `iadd`         | value1, value2 → result  | add two `int` values `value2 + value1`                                                                     |
| `idiv`         | value1, value2 → result  | divide two `int` values `value2 / value1`                                                                  |
| `imul`         | value1, value2 → result  | multiply two `int` values `value2 * value1`                                                                |
| `irem`         | value1, value2 → result  | logical `int` remainder `(value2 - ((value1 / value2) * value2))`                                          |
| `isub`         | value1, value2 → result  | `int` subtract `value2 - value1`                                                                           |
| `iand`         | value1, value2 → result  | perform a bitwise AND on two `int` values `value2 & value1`                                                |
| `ior`          | value1, value2 → result  | bitwise `int` OR `value2 | value1`                                                                         |
| `ixor`         | value1, value2 → result  | `int` xor `value2 ^ value1`                                                                                |
| `ishl`         | value1, value2 → result  | `int` shift left `value2 << value1`                                                                        |
| `ishr`         | value1, value2 → result  | `int` arithmetic shift right `value2 >> value1`                                                            |
| `iushr`        | value1, value2 → result  | `int` logical shift right `value2 >>> value1`                                                              |
| `ladd`         | value1, value2 → result  | add two `long` values `value2 + value1`                                                                    |
| `ldiv`         | value1, value2 → result  | divide two `long` values `value2 / value1`                                                                 |
| `lmul`         | value1, value2 → result  | multiply two `long` values `value2 * value1`                                                               |
| `lrem`         | value1, value2 → result  | remainder of division of two `long` values `(value2 - ((value1 / value2) * value2))`                       |
| `lsub`         | value1, value2 → result  | subtract two `long` values `value2 - value1`                                                               |
| `lshl`         | value1, value2 → result  | bitwise shift left of a `long` value1 by `int` `value2` positions `value2 << value1`                       |
| `lshr`         | value1, value2 → result  | bitwise shift right of a `long` value1 by `int` `value2` positions `value2 >> value1`                      |
| `lushr`        | value1, value2 → result  | bitwise shift right of a `long` value1 by `int` `value2` positions, unsigned `value2 >>> value1`           |
| `land`         | value1, value2 → result  | bitwise AND of two `long` values `value2 ^ value1`                                                         |
| `lor`          | value1, value2 → result  | bitwise OR of two `long` values `value2 | value1`                                                          |
| `lxor`         | value1, value2 → result  | bitwise XOR of two `long` values `value2 ^ value1`                                                         |
| `dneg`         | value → result           | negate a `double` `-value`                                                                                 |
| `fneg`         | value → result           | negate a `float` `-value`                                                                                  |
| `ineg`         | value → result           | negate `int` `-value`                                                                                      |
| `lneg`         | value → result           | negate a `long` `-value`                                                                                   |
| `dcmpg`        | value1, value2 → result  | compare two `double` values <table><tbody><tr><th>Comparison</th><th>Value</th></tr><tr><td>`value1==NaN` or<br>`value2==NaN`</td><td>`1` </td></tr><tr><td>`value1 > value2`</td><td>`1`</td></tr><tr><td>`value1==value2`</td><td>`0`</td></tr><tr><td>`value1 < value2`</td><td>`-1`</td></tr></tbody></table> |
| `dcmpl`        | value1, value2 → result  | compare two `double` values <table><tbody><tr><th>Comparison</th><th>Value</th></tr><tr><td>`value1==NaN` or<br>`value2==NaN`</td><td>`-1`</td></tr><tr><td>`value1 > value2`</td><td>`1`</td></tr><tr><td>`value1==value2`</td><td>`0`</td></tr><tr><td>`value1 < value2`</td><td>`-1`</td></tr></tbody></table> |
| `fcmpg`        | value1, value2 → result  | compare two `float` values  <table><tbody><tr><th>Comparison</th><th>Value</th></tr><tr><td>`value1==NaN` or<br>`value2==NaN`</td><td>`1` </td></tr><tr><td>`value1 > value2`</td><td>`1`</td></tr><tr><td>`value1==value2`</td><td>`0`</td></tr><tr><td>`value1 < value2`</td><td>`-1`</td></tr></tbody></table> |
| `fcmpl`        | value1, value2 → result  | compare two `float` values  <table><tbody><tr><th>Comparison</th><th>Value</th></tr><tr><td>`value1==NaN` or<br>`value2==NaN`</td><td>`-1`</td></tr><tr><td>`value1 > value2`</td><td>`1`</td></tr><tr><td>`value1==value2`</td><td>`0`</td></tr><tr><td>`value1 < value2`</td><td>`-1`</td></tr></tbody></table> |
| `lcmp`         | value1, value2 → result  | compare two `long` values   <table><tr><th>Comparison</th><th>Value</th></tr><tr><td>`value1==value2`</td><td>`0`</td></tr><tr><td>`value1 > value2`</td><td>`1`</td></tr><tr><td>`value1 < value2`</td><td>`-1`</td></tr></table> |

## Stack Manipulation

| Opcode         | Stack: [before]→[after]  | Description                              |
|----------------|--------------------------|------------------------------------------|
| `dup`          | value → value, value                                                                      | duplicate the value on top of the stack                                                                                                                                                                    |
| `dup_x1`       | value2, value1 → value1, value2, value1                                                   | insert a copy of the top value into the stack two values from the top. `value1` and `value2` must not be of the type `double` or `long`.                                                                   |
| `dup_x2`       | value3, value2, value1 → value1, value3, value2, value1                                   | insert a copy of the top value into the stack two _(if `value2` is `double` or `long` it takes up the entry of `value3`, too)_ or three values (if `value2` is neither `double` nor `long`) from the top   |
| `dup2`         | {value2, value1} → {value2, value1}, {value2, value1}                                     | duplicate top two stack words _(two values, if `value1` is not `double` nor `long`; a single value, if value1 is `double` or `long`)_     |
| `dup2_x1`      | value3, {value2, value1} → {value2, value1}, value3, {value2, value1}                     | duplicate two words and insert beneath third word _(see explanation above)_                                |
| `dup2_x2`      | {value4, value3}, {value2, value1} → {value2, value1}, {value4, value3}, {value2, value1} | duplicate two words and insert beneath fourth word                                                         |
| `pop`          | value →                                                                                   | discard the top value on the stack                                                                         |
| `pop2`         | {value2, value1} →                                                                        | discard the top two values on the stack _(or one value, if it is a `double` or `long`)_                      |
| `swap`         | value2, value1 → value1, value2                                                           | swaps two top words on the stack _(note that `value1` and `value2` must not be `double` or `long`)_          |

## Control Flow

| Opcode         | Stack: [before]→[after]  | Description                |
|----------------|--------------------------|----------------------------|
| `goto` <ul><li>label</li></ul>      | [no change]             | jump to `label`                                   |
| `if_acmpeq` <ul><li>label</li></ul> | value1, value2 →        | if references `value1 == value2`, jump to `label` |
| `if_acmpne` <ul><li>label</li></ul> | value1, value2 →        | if references `value1 != value2`, jump to `label` |
| `if_icmpeq` <ul><li>label</li></ul> | value1, value2 →        | if ints `value1 == value2`, jump to `label`       |
| `if_icmpge` <ul><li>label</li></ul> | value1, value2 →        | if ints `value1 >= value2`, jump to `label`       |
| `if_icmpgt` <ul><li>label</li></ul> | value1, value2 →        | if ints `value1 > value2`, jump to `label`        |
| `if_icmple` <ul><li>label</li></ul> | value1, value2 →        | if ints `value1 <= value2`, jump to `label`       |
| `if_icmplt` <ul><li>label</li></ul> | value1, value2 →        | if ints `value1 < value2`, jump to `label`        |
| `if_icmpne` <ul><li>label</li></ul> | value1, value2 →        | if ints `value1 != value2`, jump to `label`       |
| `ifeq` <ul><li>label</li></ul>      | value →                 | if `value == 0`, jump to `label`                  |
| `ifge` <ul><li>label</li></ul>      | value →                 | if `value >= 0`, jump to `label`                  |
| `ifgt` <ul><li>label</li></ul>      | value →                 | if `value > 0`, jump to `label`                   |
| `ifle` <ul><li>label</li></ul>      | value →                 | if `value <= 0`, jump to `label`                  |
| `iflt` <ul><li>label</li></ul>      | value →                 | if `value < 0`, jump to `label`                   |
| `ifne` <ul><li>label</li></ul>      | value →                 | if `value != 0`, jump to `label`                  |
| `ifnonnull` <ul><li>label</li></ul> | value →                 | if `value != null`, jump to `label`               |
| `ifnull` <ul><li>label</li></ul>    | value →                 | if `value == null`, jump to `label`               |
| `lookupswitch` <ul><li>pair[...]<ul><li>key</li><li>label</li></ul></li></ul>   | key →   | jump to the label associated with the given `key`, or if no such entry in the table exists jump to the default label |
| `tableswitch`  <ul><li>min</li><li>max</li><li>cases</li><li>default</li></ul>  | index → | jump to the label associated with `cases[min-index]`, or if the computed index of `min-index` is out of bounds jump to the default label |
| `athrow`       | objectref → [empty], objectref  | throws an error or exception _(notice that the rest of the stack is cleared, leaving only a reference to the `Throwable`)_ |
| `jsr` ✝ <ul><li>label</li></ul>   | value → address         | jump to `label` while also pushing the current code address to the stack _(Generally this is immediately stored in a variable at the destination)_ |
| `ret` ✝ <ul><li>var</li></ul>     | [no change]             | jump to the code offset stored in the variable `var` |

> **NOTE**: `jsr` and `ret` are deprecated instructions and are only present in classes from Java 7 or earlier. They cannot be used in Java 8 or above.

## Fields

| Opcode                  | Stack: [before]→[after] | Description                                                                    |
|-------------------------|-------------------------|--------------------------------------------------------------------------------|
| `getfield` <ul><li>owner</li><li>name</li><li>desc</li></ul>  | objectref → value       | get an instance field defined by the `owner` class and the field's `name` and `desc` |
| `getstatic` <ul><li>owner</li><li>name</li><li>desc</li></ul> | → value                 | get a static field defined by the `owner` class and the field's `name` and `desc`    |
| `putfield` <ul><li>owner</li><li>name</li><li>desc</li></ul>  | objectref, value →      | set an instance field defined by the `owner` class and the field's `name` and `desc` |
| `putstatic` <ul><li>owner</li><li>name</li><li>desc</li></ul> | value →                 | set a static field defined by the `owner` class and the field's `name` and `desc`    |

## Method Calls

| Opcode                        | Stack: [before]→[after]               | Description                                                                                                                                                |
|-------------------------------|---------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `invokeinterface` <ul><li>owner</li><li>name</li><li>desc</li></ul> | objectref, [arg1, arg2, ...] → result | invokes an interface method defined by the `owner` class and the method's `name` and `desc` on object `objectref` and puts the result on the stack _(might be `void`)_ |
| `invokespecial` <ul><li>owner</li><li>name</li><li>desc</li></ul>   | objectref, [arg1, arg2, ...] → result | invokes an instance method defined by the `owner` class and the method's `name` and `desc` on object `objectref` and puts the result on the stack _(might be `void`)_  |
| `invokestatic` <ul><li>owner</li><li>name</li><li>desc</li></ul>    | [arg1, arg2, ...] → result            | invokes a static method defined by the `owner` class and the method's `name` and `desc` and puts the result on the stack _(might be `void`)_                           |
| `invokevirtual` <ul><li>owner</li><li>name</li><li>desc</li></ul>   | objectref, [arg1, arg2, ...] → result | invokes a virtual method defined by the `owner` class and the method's `name` and `desc` on object `objectref` and puts the result on the stack _(might be `void`)_    |
| `invokespecialinterface` ✝ <ul><li>owner</li><li>name</li><li>desc</li></ul>   | objectref, [arg1, arg2, ...] → result | invokes an instance method defined by the `owner` class and the method's `name` and `desc` on object `objectref` and puts the result on the stack _(might be `void`)_  |
| `invokestaticinterface` ✝ <ul><li>owner</li><li>name</li><li>desc</li></ul>    | [arg1, arg2, ...] → result            | invokes a static method defined by the `owner` class and the method's `name` and `desc` and puts the result on the stack _(might be `void`)_                           |
| `invokevirtualinterface` ✝ <ul><li>owner</li><li>name</li><li>desc</li></ul>   | objectref, [arg1, arg2, ...] → result | invokes a virtual method defined by the `owner` class and the method's `name` and `desc` on object `objectref` and puts the result on the stack _(might be `void`)_    |

> **NOTE**: The ✝ instructions in the table above are abstractions in the Recaf assembler. These `invoke_x_interface` are variants of their respective `invoke_x` instructions with the `itf` flag set to `true`. This is normally not something you will encounter in properly compiled Java applications but has been found in some improperly processed classes in the wild _(In particular the forge modding environment)_.

## Dynamic Methods Calls

| Opcode                                                                                                                                                 | Stack: [before]→[after]   | Description                                                                                                           |
|--------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------|-----------------------------------------------------------------------------------------------------------------------|
| `invokedynamic` <ul><li>definition</li><ul><li>name, desc</li></ul><li>bootstrap handle</li><ul><li>owner, name, desc</li></ul><li>bootstrap arguments[]</li></ul> | [arg1, arg2 ...] → result | invokes a dynamic method and puts the result on the stack _(might be `void`)_. The `callsite` handles the dynamic invocation. |

> **NOTE**: Before [Java 9 - JEP 280](https://openjdk.org/jeps/280) `String` concatenation (`"a" + "b"`) used `StringBuilder` to append multiple values together. Afterwards, concatenation was reimplemented to use `invokedynamic`. This instruction is the backbone of a lot of useful language features not only in just Java, but also other JVM languages.
> Decompilers will often understand the specific usages for cases found in the standard Java language, but will often fail for third party languages or within auto-generated / obfuscated code.

## Type Conversion

| Opcode         | Stack: [before]→[after]  | Description                              |
|----------------|--------------------------|------------------------------------------|
| `d2f`          | value → result           | convert:  `double` to `float`            |
| `d2i`          | value → result           | convert:  `double` to `int`              |
| `d2l`          | value → result           | convert:  `double` to `long`             |
| `f2d`          | value → result           | convert:  `float` to `double`            |
| `f2i`          | value → result           | convert:  `float` to `int`               |
| `f2l`          | value → result           | convert:  `float` to `long`              |
| `i2b`          | value → result           | convert:  `int` to `byte`                |
| `i2c`          | value → result           | convert:  `int` to `char`                |
| `i2d`          | value → result           | convert:  `int` to `double`              |
| `i2f`          | value → result           | convert:  `int` to `float`               |
| `i2l`          | value → result           | convert:  `int` to `long`                |
| `i2s`          | value → result           | convert:  `int` to `short`               |
| `l2d`          | value → result           | convert:  `long` to `double`             |
| `l2f`          | value → result           | convert:  `long` to `float`              |
| `l2i`          | value → result           | convert:  `long` to `int`                |
| `checkcast` <ul><li>type</li></ul>  | objectref → objectref   | checks whether an `objectref` is of a certain specified `type` |
| `instanceof` <ul><li>type</li></ul> | objectref → result      | determines if an object `objectref` is of a given `type`       |

An alternative view for the primitive-to-primitive conversions:

| From → To  | int   | long  | float | double | byte  | char  | short |
| ---------- | ----- | ----- | ----- | ------ | ----- | ----- | ----- |
| **int**    | =     | `i2l` | `i2f` | `i2d`  | `i2b` | `i2c` | `i2s` |
| **long**   | `l2i` | =     | `l2f` | `l2d`  | :x:   | :x:   | :x:   |
| **float**  | `f2i` | `f2l` | =     | `f2d`  | :x:   | :x:   | :x:   |
| **double** | `d2i` | `d2l` | `d2f` | =      | :x:   | :x:   | :x:   |



## Returns

| Opcode         | Stack: [before]→[after]  | Description                |
|----------------|--------------------------|----------------------------|
| `areturn`      | objectref → [empty]      | return a reference         |
| `dreturn`      | value → [empty]          | return a `double`          |
| `freturn`      | value → [empty]          | return a `float`           |
| `ireturn`      | value → [empty]          | return an `int`            |
| `lreturn`      | value → [empty]          | return a `long`            |
| `return`       | → [empty]                | return `void` _(nothing)_  |

## Miscellaneous

| Opcode         | Stack: [before]→[after]  | Description                |
|----------------|--------------------------|----------------------------|
| `monitorenter` | objectref →                     | enter monitor for object _("grab the lock" – start of `synchronized(objectref)` section)_                  |
| `monitorexit`  | objectref →                     | exit monitor for object _("release the lock" – end of `synchronized(objectref)` section)_                  |
| `nop`          | [No change]                     | do nothing                                                                                                 |
| `line` <ul><li>line</li></ul> | [No change]      | marker that represents a `LineNumberTable` entry for the given line, beginning at the first label preceding the marker |
| `label:`       | [No change]                     | marker in bytecode for the beginning of a block of instructions; referenced by jump and switch instructions |
