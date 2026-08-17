# ConfigIconManager

The `ConfigIconManager` supplies the icons shown next to config groups, containers, and values in the config window. Icons can be registered by ID or by container, and looked up for any given `ConfigContainer` or `ConfigValue`.

The manager allows you to:

* Register an icon for a config value ID
* Register an icon for a config container ID
* Register an icon for a config group
* Look up the icon for a value, container, or group

## Registering icons

```java
// Icons are Ikon values from the Ikonli:Carbon library.
configIconManager.registerValue("my-value-id", CarbonIcons.COMPUTER);
configIconManager.registerContainer("my-container-id", CarbonIcons.SETTINGS);
configIconManager.registerGroup(ConfigGroups.SERVICE_UI, CarbonIcons.SETTINGS_ADJUST);

// Look up icons when rendering.
Ikon valueIcon = configIconManager.getValueIcon(value);
Ikon containerIcon = configIconManager.getContainerIcon(container);
Ikon groupIcon = configIconManager.getGroupIcon(container);
```

For a visual list of `CarbonIcon` values, see [Carbon Cheat Sheet](https://kordamp.org/ikonli/cheat-sheet-carbonicons.html).
