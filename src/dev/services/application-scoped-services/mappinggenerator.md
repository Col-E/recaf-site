# MappingGenerator

The mapping generator allows you to generate `Mappings` for a `Workspace` based on configurable inputs:

* Filter what classes, fields, and methods should be included in the generated output mappings via a chain of `NameGeneratorFilter` items
* Control the naming scheme of classes, fields, and methods via an implementation of `NameGenerator`

## Filtering what to generate mappings for

What we generate mappings for is controlled by a linked-list of `NameGeneratorFilter` items. Each item in the chain can generalized to *"include this"* or *"exclude this"*. Here is an example:

```java
@Inject
StringPredicateProvider strMatchProvider;

// [Access blacklist 'public, protected'] --> [Class whitelist 'com/example/']
//  Any non-public/protected class/field/method in 'com/example' will have a name generated
IncludeClassesFilter includeClasses = new IncludeClassesFilter(null /* tail of linked list */, strMatchProvider.newStartsWithPredicate("com/example/"));
ExcludeModifiersNameFilter excludePublicProtected = new ExcludeModifiersNameFilter(includeClasses, Arrays.asList(Opcodes.ACC_PUBLIC | Opcodes.ACC_PROTECTED), true, true, true);

// Use 'excludePublicProtected' as the 'NameGeneratorFilter' to pass as your filter - It is the head of the linked list.
```

You can use any of the existing `NameGeneratorFilter` implementations in the `software.coley.recaf.services.mapping.gen.filter` package, or make your own.

## Controlling the naming scheme

There are a few simple implementations of `NameGenerator` which can be used as-is, but for more advanced control you'll probably want to make your own. The interface outlines one method for naming each kind of item. Here is a simple implementation:

```java
NameGenerator nameGenerator = new NameGenerator() {
    @Nonnull
    @Override
    public String mapClass(@Nonnull ClassInfo info) {
        return "mapped/Class" + Math.abs(info.getName().hashCode());
    }
    @Nonnull
    @Override
    public String mapField(@Nonnull ClassInfo owner, @Nonnull FieldMember field) {
        return "mappedField" + Math.abs(owner.getName().hashCode() + info.getName().hashCode());
    }
    @Nonnull
    @Override
    public String mapMethod(@Nonnull ClassInfo owner, @Nonnull MethodMember method) {
        return "mappedMethod" + Math.abs(owner.getName().hashCode() + info.getName().hashCode());
    }
};
```

## Generating the output `Mappings`

Once you have a `NameGenerator` and `NameGeneratorFilter` pass them along to `generate(Workspace, WorkspaceResource, InheritanceGraph, NameGenerator, NameGeneratorFilter)`. The method takes in the `Workspace` and the `WorkspaceResource` containing classes you want to generate mappings for. The `WorkspaceResouerce` will almost always be the workspace's primary resource.

```java
@Inject
InheritanceGraph inheritanceGraph; // You need the inheritance graph associated with the workspace.

Mappings mappings = mappingGenerator.generate(workspace, resource, inheritanceGraph, nameGenerator, filter);
```