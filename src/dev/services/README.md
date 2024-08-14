# Services

As Recaf is driven by CDI, almost all of its features are defined as `@Inject`-able service classes.

- [`@ApplicationScoped` services](application-scoped-services/index.md): Any feature injectable regardless of whether or not a `Workspace` is currently open
- [`@WorkspaceScoped services`](workspace-scoped-services/index.md): Any feature injectable only when a `Workspace` is currently open