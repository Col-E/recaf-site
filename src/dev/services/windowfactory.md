# WindowFactory

The `WindowFactory` creates anonymous JavaFX `Stage` windows with Recaf's default window behavior applied.

The service allows you to:

* Create an anonymous stage from a `Scene`, with a title and minimum size.

## Creating a stage

```java
// Title can be bound to an observable value
Stage stage = windowFactory.createAnonymousStage(scene, titleBinding, 800, 600);

// Or a plain string title
Stage stage = windowFactory.createAnonymousStage(scene, "My window", 800, 600);
stage.show();
```
