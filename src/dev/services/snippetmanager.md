# SnippetManager

The snippet manager is used to store common snippets of assembler text that users can copy and paste into the assembler UI.

## Snippets

Snippets are a simple record with three components:

- `name` - The name, also used as the key for snippet manager operations.
- `description` - The optional explanation of what the snippet is for. If no such value is given this should be an empty string.
- `content` - The actual snippet body

## Getting current snippets

```java
// Snapshot of existing snippets
List<Snippet> snippets = snippetManager.getSnippets();

// Getting a snippet by name
Snippet example = snippetManager.getByName("example");
```

## Registering / unregistering snippets

```java
// Create and register 'System.out.println("Hello")'
String content = """
        getstatic java/lang/System.out Ljava/io/PrintStream;
        ldc "hello"
        invokevirtual java/io/PrintStream.println (Ljava/lang/String;)V
        """;
snippetManager.putSnippet(new Snippet("hello", "prints 'hello'", content));

// Unregistering it
snippetManager.removeSnippet("hello");
```

## Listening to the creation/removal/modification of snippets

```java
SnippetListener listener = new SnippetListener() {
	@Override
	public void onSnippetAdded(@Nonnull Snippet snippet) {
		System.out.println("NEW: " + snippet.name());
	}
	@Override
	public void onSnippetModified(@Nonnull Snippet old, @Nonnull Snippet current) {
		System.out.println("MOD: " + old.name());
	}
	@Override
	public void onSnippetRemoved(@Nonnull Snippet snippet) {
		System.out.println("DEL: " + snippet.name());
	}
};
snippetManager.addSnippetListener(listener);
snippetManager.removeSnippetListener(listener);
```

