# Script examples

The [scripts](scripts.md) page covers the two script forms. This page is a set of starting points for common reverse-engineering jobs.

Pass `false` as the first argument to `workspace.findClasses` so Recaf's internal support classes are skipped. Shorthand scripts already have `workspace` and `log`. Full-class scripts that need extra services declare an `@Inject` constructor, same as on the scripts page.

## Find classes by string or method signature

Looks through the open workspace for classes that contain a constant-pool string, a method with a given descriptor, or both.

```java
// ==Metadata==
// @name Find classes
// @description Find classes by string constant and/or method descriptor
// @version 1.0.0
// @author Author
// ==/Metadata==

if (workspace == null) return;

String needle = "license expired";
String descriptor = "(Ljava/lang/String;)V";

workspace.findClasses(false, c -> {
    boolean hasString = c.isJvmClass() && c.asJvmClass().getStringConstants().contains(needle);
    boolean hasDescriptor = c.getMethods().stream()
            .anyMatch(m -> m.getDescriptor().equals(descriptor));
    return hasString || hasDescriptor;
}).forEach(path -> log.info("{}", path.getValue().getName()));
```

## Rename fields by type and declaration order

Builds mappings for every `String` field, named `string0`, `string1`, … in class-file order, then applies them through [MappingApplierService](../services/mappingapplierservice.md) so references update with the names.

```java
// ==Metadata==
// @name Rename string fields
// @description Rename String fields by declaration order
// @version 1.0.0
// @author Author
// ==/Metadata==

import jakarta.enterprise.context.Dependent;
import jakarta.inject.Inject;
import org.slf4j.Logger;
import software.coley.recaf.analytics.logging.Logging;
import software.coley.recaf.info.JvmClassInfo;
import software.coley.recaf.info.member.FieldMember;
import software.coley.recaf.path.ClassPathNode;
import software.coley.recaf.services.mapping.IntermediateMappings;
import software.coley.recaf.services.mapping.MappingApplier;
import software.coley.recaf.services.mapping.MappingApplierService;
import software.coley.recaf.services.workspace.WorkspaceManager;
import software.coley.recaf.workspace.model.Workspace;

@Dependent
public class RenameStringFieldsScript {
    private static final Logger logger = Logging.get("rename-string-fields");
    private final MappingApplierService mappingApplierService;
    private final WorkspaceManager workspaceManager;

    @Inject
    public RenameStringFieldsScript(MappingApplierService mappingApplierService, WorkspaceManager workspaceManager) {
        this.mappingApplierService = mappingApplierService;
        this.workspaceManager = workspaceManager;
    }

    public void run() {
        if (!workspaceManager.hasCurrentWorkspace()) {
            logger.error("No workspace is open");
            return;
        }
        Workspace workspace = workspaceManager.getCurrent();

        IntermediateMappings mappings = new IntermediateMappings();
        for (ClassPathNode path : workspace.findClasses(false, c -> c.isJvmClass())) {
            JvmClassInfo cls = path.getValue().asJvmClass();
            int index = 0;
            for (FieldMember field : cls.getFields()) {
                if (!field.getDescriptor().equals("Ljava/lang/String;"))
                    continue;
                mappings.addField(cls.getName(), field.getDescriptor(), field.getName(), "string" + index);
                index++;
            }
        }

        if (mappings.isEmpty()) {
            logger.info("No String fields to rename");
            return;
        }

        MappingApplier applier = mappingApplierService.inCurrentWorkspace();
        if (applier == null) {
            logger.error("No workspace is open");
            return;
        }
        applier.applyToPrimaryResource(mappings).apply();
        logger.info("Renamed String fields in declaration order");
    }
}
```

## Modify matching method bodies

Walks methods whose name starts with `check` and rewrites string constants in those methods. Swap the name prefix and replacement for whatever you are patching.

```java
// ==Metadata==
// @name Patch check methods
// @description Replace string constants in methods named check*
// @version 1.0.0
// @author Author
// ==/Metadata==

if (workspace == null) return;

for (var path : workspace.findClasses(false, c -> c.isJvmClass()
        && c.getMethods().stream().anyMatch(m -> m.getName().startsWith("check")))) {
    JvmClassInfo cls = path.getValue().asJvmClass();
    JvmClassBundle bundle = path.getValueOfType(JvmClassBundle.class);
    if (bundle == null) continue;

    ClassReader reader = cls.getClassReader();
    ClassWriter writer = new ClassWriter(reader, 0);
    reader.accept(new ClassVisitor(RecafConstants.getAsmVersion(), writer) {
        @Override
        public MethodVisitor visitMethod(int access, String name, String desc, String signature, String[] exceptions) {
            MethodVisitor mv = super.visitMethod(access, name, desc, signature, exceptions);
            if (!name.startsWith("check"))
                return mv;
            return new MethodVisitor(RecafConstants.getAsmVersion(), mv) {
                @Override
                public void visitLdcInsn(Object value) {
                    if ("denied".equals(value))
                        value = "ok";
                    super.visitLdcInsn(value);
                }
            };
        }
    }, cls.getClassReaderFlags());
    bundle.put(cls.toJvmClassBuilder().adaptFrom(writer.toByteArray()).build());
}
```

## Make methods no-op

This is the same operation as **Make no-op** / **Simple return value** in the method context menu. `MethodNoopingVisitor` replaces the matched method bodies with a default return (`return;`, `return 0;`, `return null;`, empty collections, and so on).

```java
// ==Metadata==
// @name No-op verify methods
// @description Replace verify()Z method bodies with a default return
// @version 1.0.0
// @author Author
// ==/Metadata==

if (workspace == null) return;

for (var path : workspace.findClasses(false, c -> c.isJvmClass()
        && c.getMethods().stream().anyMatch(m -> m.getName().equals("verify")
        && m.getDescriptor().equals("()Z")))) {
    JvmClassInfo cls = path.getValue().asJvmClass();
    JvmClassBundle bundle = path.getValueOfType(JvmClassBundle.class);
    if (bundle == null) continue;

    var targets = cls.getMethods().stream()
            .filter(m -> m.getName().equals("verify") && m.getDescriptor().equals("()Z"))
            .toList();

    ClassReader reader = cls.getClassReader();
    ClassWriter writer = new ClassWriter(reader, 0);
    reader.accept(new MethodNoopingVisitor(writer, MethodPredicate.of(targets)), cls.getClassReaderFlags());
    bundle.put(cls.toJvmClassBuilder().adaptFrom(writer.toByteArray()).build());
    log.info("No-op'd verify()Z in {}", cls.getName());
}
```

For a specific return value instead of the default (for example `return true` on a `boolean` method), inject `StubbingService` in a full-class script and call `stubMethods` with one of `getStubbings(returnDescriptor)`.
