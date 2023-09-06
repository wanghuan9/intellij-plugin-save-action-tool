# Save Actions Plugin

Supports configurable, Eclipse like, save actions, including "optimize imports", "reformat code", "rearrange code", "compile file" and some quick fixes like "add / remove 'this' qualifier", etc. The plugin executes the configured actions when the file is synchronised (or saved) on disk.

Using the save actions plugin makes your code cleaner and more uniform across your code base by enforcing your code style and code rules every time you save. The settings file (see [files location](#files-location)) can be shared in your development team so that every developer has the same configuration.

The code style applied by the save actions plugin is the one configured your settings at "File > Settings > Editor > Code Style". For some languages, custom formatter (Dartfmt, Prettier, etc.) may also be triggered by the save actions plugin. See the [Editor Actions](#editor-actions) configuration for more information.

Thank you to JetBrains for supporting the Save Actions plugin: they provide an open-source license, which is necessary to build, test and deploy this plugin. Check out their products at [https://www.jetbrains.com](https://www.jetbrains.com/?from=intellij-save-actions-plugin).

<p align="center">
  <img alt="Save Actions Plugin icon light" title="Save Actions Plugin icon light" src="./docs/icon-save-actions.svg" width="200" height="200">
</p>

## Content

- [Features](#features)
- [Installation](#installation)
- [Usage](#usage)
- [Configuration](#configuration)
- [Compatibility](#compatibility)
- [Files location](#files-location)
- [Contributions](#contributions)
- [Links](#links)
- [Licence](#licence)

## Features

### All JetBrains products

- Optimize imports
- Run on file save, shortcut, batch (or a combination)
- Run on multiple files by choosing a scope
- Reformat code (whole file or only changed text)
- Rearrange code (reorder methods, fields, etc.)
- Include / exclude files with regex support
- Works on any file type (Java, Python, XML, etc.)
- Launch any editor action using "quick lists"
- Uses a settings file per project you can commit (see [Files location](#files-location))
- Available keymaps and actions for activation (see [Keymap and actions](#keymap-and-actions))

![Save actions plugin settings page](https://github.com/dubreuia/intellij-plugin-save-actions/blob/main/docs/intellij-save-actions-plugin-settings-page.png)

### Java IDE products

Works in JetBrains IDE with Java support, like Intellij IDEA and AndroidStudio.

- Compile project after save (if compiling is available)
- Reload debugger after save (if compiling is available)
- Eclipse configuration file `.epf` support (see [Eclipse support](#eclipse-support))
- Automatically fix Java inspections (see [Java quick fixes](#java-fixes))

![Save actions plugin settings page for Java](https://github.com/dubreuia/intellij-plugin-save-actions/blob/main/docs/intellij-save-actions-plugin-settings-page-java.png)

## Installation

### IDE (recommended)

Install it from your IDE (Intellij IDEA, PyCharm, etc.):

- "File > Settings > Plugins > Browse repositories... > Search 'Save Actions' > Category 'Code tools'"

### JetBrains plugin repository

All versions of the plugin are available from the [JetBrains plugin repository](https://plugins.jetbrains.com/plugin/7642). You can download the jar and add it to your IDE (you won't get updates thought):

- "File > Settings > Plugins > Install plugin from disk..."

## Usage

The plugin can trigger automatically or manually on IDE actions (standard actions) or plugin actions. Most actions needs to be enabled individually (see [activation](#activation)).

### IDE actions

The plugin will trigger automatically on any of these IDE actions (needs to be activated with "Activate save actions on file save" in [activation](#activation))):

- **Frame deactivation**, which is when the editor loses focus, configured in "File > Settings > Appearance & Behavior > System Settings > Save files on frame deactivation" .
- **Application idle**, which is when the IDE isn't used for a period of time, configured in "File > Settings > Appearance & Behavior > System Settings > Save files automatically if application is idle for x sec".
- **Save All**, which is bound by default to "CTRL + S". Some IDE might use "CTRL + S" for the single **Save Document** action, on which the plugin will NOT trigger. This is by design, see issue [#222](https://github.com/dubreuia/intellij-plugin-save-actions/issues/222).

### Plugin actions

The plugin actions are grouped under the menu "Code > Save Actions". You can associate a keymap to any action in "Settings > Keymap > Search 'save actions'".

- **Enable Save Actions (default: not binded)** will activate or deactivate the plugin by changing the configuration.
- **Execute Save Actions on shortcut (default: "CTRL + SHIFT + S")** will trigger the plugin manually (needs to be activated with "Activate save actions on shortcut" in [activation](#activation)).
- **Execute Save Actions on multiple files (default: not binded)** will show a popup to select the files (or a scope) on which to trigger the plugin (needs to be activated with "Activate save actions on batch" in [activation](#activation)).

<p align="center">
  <img alt="Save Actions Plugin Action menu" title="Save Actions Plugin Action menu" src="./docs/intellij-save-actions-plugin-action-menu.png" width="371" height="77">
</p>

## Configuration

The configurations are located in "File > Settings > Other Settings > Save Actions".

### Activation

You can quickly toggle the plugin activation by using the "Enable Save Action" action. Use "CTRL + SHIFT + A" then search for it. It will also show if it is currently activated or not.

| Name                               | Description
| ---                                | ---
| Activate save actions on file save | Enable / disable the plugin on file save. Before saving each file, it will perform the configured actions below
| Activate save actions on shortcut  | Enable / disable the plugin on shortcut, by default "CTRL + SHIFT + S" (configured in "File > Keymaps > Main menu > Code > Save Actions")
| Activate save actions on batch     | Enable / disable the plugin on batch, by using "Code > Save Actions > Execute on multiple files"
| No action if compile errors        | Enable / disable no action if there are compile errors. Applied to each file individually

### Global

| Name                               | Description
| ---                                | ---
| Optimize imports                   | Enable / disable import organization (configured in "File > Settings > Code Style > Java > Imports")
| Reformat file                      | Enable / disable formatting (configured in "File > Settings > Code Style"). See "Reformat only changed code" for more options
| Reformat only changed lines        | Enable / disable formatting for only changed lines, which will work only if a VCS is configured
| Rearrange fields and methods       | Enable / disable re-ordering of fields and methods (configured in "File > Settings > Code Style > Java > Arrangement")

### Build

| Name                               | Description
| ---                                | ---
| *\[experimental\]* Compile file    | Enable / disable compiling of the modified file. The compiler might compile other files as well. **Warning: this feature is experimental, please post feedback in the github issues**
| *\[experimental\]* Reload file     | Enable / disable reloading of the files in the running debugger, meaning the files will get compiled first. The compiler might compile other files as well. **Warning: this feature is experimental, please post feedback in the github issues**
| *\[experimental\]* Execute action  | Enable / disable executing of an action using quick lists (using quick lists at "File > Settings > Appearance & Behavior > Quick Lists"). See [Editor Actions](#editor-actions) for more information **Warning: this feature is experimental, please post feedback in the github issues**

#### Editor Actions

Some languages requires specific actions, such as Dartfmt or Prettier:

- For Dart developers, enable "Use the dartfmt tool when formatting the whole file" option in "File > Settings > Editor > Code Style > Dart > Dartfmt".
- For Prettier (https://prettier.io/) users, read below.

Using the "Execute action" configuration, the plugin can launch arbitrary editor actions. While not all actions will work, it can be used to launch external tools, specific runs, etc. This feature is experimental, you can post your feedback on issue [#118](https://github.com/dubreuia/intellij-plugin-save-actions/issues/118).

The actions are implemented in the form of "quick lists", an IDE function that is used to define a list of actions that can be then executed. Quick lists can be configured at "File > Settings > Appearance & Behavior > Quick Lists", and once configured, one can be selected and used in the plugin, using the "Execution action" configuration drop down list.

### File

| Name                               | Description
| ---                                | ---
| File path inclusions               | Add / remove file path inclusions (by default, everything included). The Java regular expressions match the whole file name from the project root. Include only Java files: `.*\.java`. 
| File path exclusions               | Add / remove file path exclusions to ignore files (overrides inclusions). The Java regular expressions match the whole file name from the project root. Exclude 'Main.java' only in root folder: `Main\.java`. Exclude file 'Foo.java' only in folder 'src': `src/Foo\.java`. Exclude all xml files in any folder: `.*/.*\.xml`
| Use external Eclipse configuration | Add external configuration file ".epf" to read settings from. This will update the current settings and use only the ".epf" file content. Use "reset" button to remove

### Java fixes

If a quick fix adds something that is removed by another quick fix, the removal wins.

| Name                                                                     | Description
| ---                                                                      | ---
| Add final modifier to field                                              | The field `private int field = 0` becomes `private final int field = 0`
| Add final modifier to local variable or parameter                        | The local variable `int variable = 0` becomes `final int variable = 0`
| Add final modifier to local variable or parameter except if implicit     | The local variable `int variable = 0` becomes `final int variable = 0`, but not if it is implicit like in try with resources `try (Resource r = new Resource())`
| Add static modifier to methods                                           | The method `private void method()` becomes `private static void method()` if the content does not references instance fields
| Add this to field access                                                 | The access to instance field `field = 0` becomes `this.field = 0`
| Add this to method access                                                | The access to instance method `method()` becomes `this.method()`
| Add class qualifier to static member access                              | The access to class field `FIELD = 0` becomes `Class.FIELD` for a class named Class. Exclusive with "Add class qualifier to static member access outside declaring class only".
| Add class qualifier to static member access outside declaring class only | The access to class field `FIELD = 0` becomes `Class.FIELD` for a class named class, but only if the static member is outside declaring class. Exclusive with "Add class qualifier to static member access".
| Add missing @Override annotations                                        | The method `void method()` becomes `@Override void method()` if it overrides a method from the parent class
| Add blocks to if/while/for statements                                    | The statement `if (true) return false` becomes `if (true) { return false; }` (a block), also working for `for` and `while` statements
| Add missing serialVersionUID field for Serializable classes              | The class `class Class implements Serializable` will get a new field `private static final long serialVersionUID` with generated serial version uid
| Remove unnecessary this to field and method                              | The access to instance field `this.field = 0` becomes `field = 0`, also working for methods
| Remove final from private method                                         | The method `private final void method()` becomes `private void method()`
| Remove unnecessary final to local variable or parameter                  | The local variable `int final variable = 0` becomes `int variable = 0`
| Remove explicit generic type for diamond                                 | The list creation `List<String> list = new ArrayList<String>()` becomes `List<String> list = new ArrayList<>()`
| Remove unused suppress warning annotation                                | The annotation `@SuppressWarning` will be removed if it is unused (warning: "unchecked" doesn't work properly see [#87](https://github.com/dubreuia/intellij-plugin-save-actions/issues/87))
| Remove unnecessary semicolon                                             | The statement `int variable = 0;;` becomes `int variable = 0;`
| Remove blocks from if/while/for statements                               | The statement `if (true) { return false; }` becomes `if (true) return false;`, also working for `for` and `while` statements
| Change visibility of field or method to lower access                     | The field `public int field = 0` becomes `private int field = 0` if it is not used outside class, also working for methods


### JetBrains plugin page

The plugin is in the [JetBrains plugin repository](https://plugins.jetbrains.com/plugin/21538-save-actions-tool), please take the time to [rate it](https://plugins.jetbrains.com/plugin/21538-save-actions-tool)! 

## Licence

[MIT License](LICENSE.txt)

