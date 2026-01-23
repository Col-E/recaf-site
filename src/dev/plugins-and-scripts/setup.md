# Setup for making plugins & scripts

## For plugins

When writing plugins you need a development environment to compile against Recaf's API. You can find a template project for creating plugins on GitHub at [Recaf-Plugins/Recaf-4x-plugin-workspace](https://github.com/Recaf-Plugins/Recaf-4x-plugin-workspace). Information on setting up the project can be found in the readme. The plugin workspace also is set up to provide an easy way to quickly run Recaf with your plugin without having to manually build and copy the resulting jar to the Recaf plugins directory via `gradlew runRecaf`.

## For scripts

When writing scripts it is **strongly** recommended to create them in a development environment with Recaf on the classpath *(in source or binary form)*. Technically speaking you could make a script with just a simple text editor, but when you write Java its really ideal if you do so in an IDE like IntelliJ. You can use the same plugin workspace template project to create scripts. Instead of building a plugin, just copy the source of your script to Recaf's plugin folder when you're done. Alternatively, you can follow the same idea but instead of using the plugin workspace project you can use the actual Recaf project.