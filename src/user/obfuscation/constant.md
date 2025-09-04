# Constant Obfuscation

Generally when you write an application, having sensitive data like an API key in the client source code is a bad idea... but sometimes it cannot be avoided. In such cases its a wise idea to use constant obfuscation, which transforms single values like numbers, strings, and other simple values into more obscure forms. 

## Example: Strings

Strings often contain information that can be used as landmarks in obfuscated code. For instance, you can search for `skeleton.hurt` in an obfuscated Minecraft jar and it will lead you to the `EntitySkeleton.getHurtSound()` method in older versions of the game. Normally, the names are obfuscated but it can be a starting point to begin mapping the obfuscated names back to clear names. To prevent this, any form of obfuscation on the string that breaks a simple search will suffice. Here are some examples.

Splitting the string into parts:

```java
new StringBuilder("s").append("kel").append("eto").append("b.hu").append("rt").toString();

"s".concat("kel").concat("eto").concat("b.hu").concat("rt");

"\0.\1".replace("\0", "skeleton").replace("\1", "hurt");
```

Manipulating character values:

```java
xor("ltzszkpq1wjmk", 0b1111);

static String xor(String in, int i) {
    StringBuilder out = new StringBuilder();
    for (char c : in.toCharArray())
        out.append((char) (c ^ i));
    return out.toString();
}
```

Breaking into an array or general construction via an array of bytes/characters:

```java
new String(new byte[] {115, 107, 101, 108, 101, 116, 111, 110, 46, 104, 117, 114, 116});
```

## Example: Primitives

Primitives like `byte`, `short`, `char`, `int`, and `long` generally don't have as much value for reverse engineers as Strings but it is still common to see these obfuscated. The floating point primitives `float` and `double` can be obfuscated but its generally rare to see this. Usually obfuscated primitives are done so locally within method code with a series of mathematical operations such as `+`, `-`, `*`, `&`, `~`, `^`, `<<`, `>>` and `|`, but there's nothing stopping you from being a little bit more creative than that.

```java
int width = 800;
int height = 600;

// obfuscated
int width = (1600 >> 2) * (1 << 1);
int height = 18 + (((0b101010100 ^ 0b1100) << 2) % 197) * 3;

// also obfuscated
int width = new Random(411294766).nextInt(10000);
int height = -7 + ("sad".hashCode() & 0b1000000000) + ("cat".hashCode() >> 10);
```

## Cleaning up constant obfuscation with Recaf

See the following page:  [Transformers](..\deobfuscation\transformers.md) 
