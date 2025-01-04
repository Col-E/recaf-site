# MappingFormatManager

The mapping format manager tracks recognized mapping formats, allowing you to:

- Iterate over the names of formats supported
- Create instances of `MappingFileFormat` based on the supported format name
  - Used to parse mapping files into Recaf's `IntermediateMappings` model, which can be used in a variety of other mapping APIs.
- Register new mapping file formats

## Iterating over recognized formats

To find out the types of mapping files Recaf supports, invoke `getMappingFileFormats()` to get a `Set<String>` of the supported file format names.

```java
// The names in the returned set can be used to create instances of mapping file formats
Set<String> formats = mappingFormatManager.getMappingFileFormats();
```

## Creating `MappingFileFormat` instances

To create an instance of a `MappingFileFormat`, invoke `createFormatInstance(String)` with the name of the supported file format.

```java
// Read contents of file
String fabricMappingContents = Files.readString(Paths.get("yarn-1.18.2+build.2-tiny"));

// Create the mapping format for some recognized type, then parse the mapping file's contents
MappingFileFormat format = mappingFormatManager.createFormatInstance("Tiny-V1");
IntermediateMappings parsedMappings = format.parse(fabricMappingContents);

// Do something with your parsed mappings
```

## Registering new `MappingFileFormat` instances

To register a new kind of mapping file format, invoke `registerFormat(String, Supplier<MappingFileFormat>)`. The name argument should match the `MappingFileFormat.implementationName()` of the registered format implementation.

Given this example implementation of a `MappingFileFormat`:
```java
import jakarta.annotation.Nonnull;
import software.coley.recaf.services.mapping.format.AbstractMappingFileFormat;
import software.coley.recaf.services.mapping.format.InvalidMappingException;

public class ExampleFormat extends AbstractMappingFileFormat {
    public ExampleFormat() {
        super("CustomFormat", /* supportFieldTypeDifferentiation */ true,  /* supportVariableTypeDifferentiation */ true);
    }

    @Nonnull
    @Override
    public IntermediateMappings parse(@Nonnull String mappingsText) throws InvalidMappingException {
        IntermediateMappings mappings = new IntermediateMappings();

        String[] lines = mappingsText.split("\n");
        for (String line : lines) {
            // 0     1               2
            // class obfuscated-name clean-name
            if (line.startsWith("class\t")) {
                String[] columns = line.split("\t");
                String obfuscatedName = columns[1];
                String cleanName = columns[2];

                // Add class mapping to output
                mappings.addClass(obfuscatedName, cleanName);
            }

            // 0      1                          2               3               4
            // member obfuscated-declaring-class obfuscated-name obfuscated-desc clean-name
            if (line.startsWith("member\t")) {
                String[] columns = line.split("\t");
                String obfuscatedDeclaringClass = columns[1];
                String obfuscatedDesc = columns[3]; // If 'supportFieldTypeDifferentiation == false' then this would be null
                String obfuscatedName = columns[2];
                String cleanName = columns[4];

                // Add field mapping to output
                if (obfuscatedDesc.charAt(0) == '(')
                    mappings.addMethod(obfuscatedDeclaringClass, obfuscatedDesc, obfuscatedName, cleanName);
                else
                    mappings.addField(obfuscatedDeclaringClass, obfuscatedDesc, obfuscatedName, cleanName);
            }
        }

        return mappings;
    }
}
```

It should be registered with:
```java
mappingFormatManager.registerFormat("CustomFormat", ExampleFormat::new);
```