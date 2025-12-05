# Flow Obfuscation

Flow obfuscation aims to make the logical execution order of the application more confusing.

## Example: Opaque predicate

Before:
```java
public void print() {
    System.out.println("A");
}
```

After:
```java
private static final int j;

public void print() {
	if (j != 0) System.out.println(j == 2 << 0x10 ? "B" : "A");
}

static {
    j++;
}
```

## Example: Switch

Before:
```java
public void print() {
    System.out.println("A");
}
```

After
```java
@Override
public void print() {
    PrintStream ps = System.out;

    int i = -1;
    i++;
    String k = "0";
    while (true) {
        switch (Integer.parseInt(k)) {
            case 0:
                i += 7;
                k = "28";
                break;
            case 7:
                k = "A";
                break;
            default:
                ps.println(k);
                return;
        }
    }
}
```

## Cleaning up flow obfuscation with Recaf

See the following page:  [Transformers](../deobfuscation/transformers.md) 