# Name Obfuscation

Obfuscation that changes the names of classes, fields, and methods is commonly referred to as name obfuscation, or identifier renaming. There are plenty of different ways to name things that cause reverse engineering to be more challenging.

## Limitations of name obfuscation

[JVMS 4.2](https://docs.oracle.com/javase/specs/jvms/se20/html/jvms-4.html#jvms-4.2) defines what characters are not allowed to appear in different kinds of names.

> Class and interface names that appear in class file structures are always represented in a fully qualified form known as binary names. For historical reasons, the syntax of binary names that appear in class file structures differs from the syntax of binary names documented in JLS §13.1. In this internal form, the ASCII periods (`.`) that normally separate the identifiers which make up the binary name are replaced by ASCII forward slashes (`/`). The identifiers themselves must be unqualified names.
>
> Names of methods, fields, local variables, and formal parameters are stored as unqualified names. An unqualified name must contain at least one Unicode code point and must not contain any of the ASCII characters `. ; [ /` _(that is, period or semicolon or left square bracket or forward slash)_. 
>
> Method names are further constrained so that, with the exception of the special method names `<init>` and `<clinit>`, they must not contain the ASCII characters `<` or `>` _(that is, left angle bracket or right angle bracket)_. 

Aside from these few restrictions, the sky is the limit.

## Examples

The following name obfuscation strategies will target this basic data model class:

```java
public class User {
    private String username;
    private int userId;

    public User(String username, int userId) {
        this.username = username;
        this.userId = userId;
    }

    public void displayUserInfo() {
        System.out.println("User: " + username + ", ID: " + userId);
    }

    public static void main(String[] args) {
        User user = new User("Alice", 12345);
        user.displayUserInfo();
    }
}
```

### Short & overloaded naming

Obfuscators like [ProGuard](https://github.com/Guardsquare/proguard) will rename as many things as possible to the same short names. This has two main benefits.

1. It makes it difficult to determine what is being referred to when looking at decompiler output since things are only referred to by name.
2. It saves space in the constant pool, which makes the class file smaller. Instead of having five separate entries for `User`, `username`, `userId`, `user`, and `displayUserInfo` you now only have one entry for `a`.

```java
// User --> a
public class a {
	// username + userId --> a
	// As long as the types of multiple fields are unique, they can share the same name
    private String a;
    private int a;

	// Parameters & local variables can be named anything 
	// since they are debugger metadata not required for much else at runtime.
    public a(String a, int a) {
    	// Because the types are unique, but names are shared its impossible to tell what is
    	// assigned to what here just by looking at decompiler output
        this.a = a;
        this.a = a;
    }

    public void a() {
    	// If you're lucky the decompiler will hint which field is referenced in ambiguous cases
    	// by casting to the field's type.
        System.out.println("User: " + (String) a + ", ID: " + (int) a);
    }

    public static void main(String[] a) {
        a a = new a("Alice", 12345);
        a.a();
    }
}
```

### Reserved keyword naming

Identifiers can be mapped to reserved keywords such as primitives (`int`, `float`, etc), access modifiers (`private`, `public`, etc) and other language features such as `switch`, `for`, etc. This is generally annoying as it messes with syntax highlighting of tools and confuses Java source code parsers.

> Note: In this case, all identifiers are given unique keywords, but the same principle as discussed before can be applied. You could very well name every identifier in the example `void` like how the prior example named every identifier `a`.

```java
public class void {
    private String float;
    private int int;

    public void(String short, int byte) {
        this.float = short;
        this.int = byte;
    }

    public void long() {
        System.out.println("User: " + float + ", ID: " + int);
    }

    public static void main(String[] private) {
        void char = new void("Alice", 12345);
        char.long();
    }
}
```

### I and L naming

The letters `I` and `l` in some font families look very similar. Some obfuscators take advantage of this by naming identifiers with a series of `I` and `l` in the hopes that all identifiers visually look identical. For instance:

- `IIlII`
- `IlIIl`
- `lIIlI`

With a good font, these will be easily identifiable as separate names.

```java
public class IIlII {
    private String IlIIl;
    private int lIIlI;

    public IIlII(String IlIIl, int lIIlI) {
        this.IlIIl = IlIIl;
        this.lIIlI = lIIlI;
    }

    public void IIIlI() {
        System.out.println("User: " + IlIIl + ", ID: " + lIIlI);
    }

    public static void main(String[] IIIII) {
        IIlII llIll = new IIlII("Alice", 12345);
        llIll.IIIlI();
    }
}
```

### Empty space naming

There are plenty of unicode letters that look like empty spaces. Combining several of these together will let an obfuscator make classes look largely empty.

```java
public class  {
    private String ;
    private int ;

    public (String , int ) {
        this. = ;
        this. = ;
    }

    public void () {
        System.out.println("User: " +  + ", ID: " + );
    }

    public static void main(String[] ) {
          = new ("Alice", 12345);
        .();
    }
}
```

### Windows reserved naming

Name a class `CON` in any variation of capitalization on a Windows computer and see what happens.

## Cleaning up names with Recaf

See the following page:  [Mapping](../deobfuscation/mapping.md)
