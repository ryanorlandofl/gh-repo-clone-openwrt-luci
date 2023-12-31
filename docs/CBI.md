# Writing LuCI CBI models

See [online wiki](https://github.com/openwrt/luci/wiki/CBI) for latest version.

CBI models are Lua files describing the structure of an UCI config file and the resulting HTML form to be evaluated by the CBI parser.<br />
All CBI model files must return an object of type `luci.cbi.Map`.<br />
For a commented example of a CBI model, see the [Writing Modules tutorial](./ModulesHowTo.md).

The scope of a CBI model file is automatically extended by the contents of the module `luci.cbi` and the `translate` function from `luci.i18n`.

This Reference covers **the basics** of the CBI system.


## class Map (config, title, description)
This is the root object of the model.

* `config:` configuration filename to be mapped, see [UCI documentation](https://openwrt.org/docs/guide-user/base-system/uci) and the files in `/etc/config`
* `title:` title shown in the UI
* `description:` description shown in the UI

#### function :section (sectionclass, ...)
Creates a new section
* `sectionclass`: a class object of the section
* _additional parameters passed to the constructor of the section class_

----

## class NamedSection (name, type, title, description)
An object describing an UCI section selected by the name.<br />
To instantiate use: `Map:section(NamedSection, "name", "type", "title", "description")`

* `name:` UCI section name
* `type:` UCI section type
* `title:` The title shown in the UI
* `description:` description shown in the UI

#### function :option(optionclass, ...)
Creates a new option
* `optionclass:` a class object of the section
* _additional parameters passed to the constructor of the option class_

#### property .addremove = false
Allows the user to remove and recreate the configuration section.

#### property .dynamic = false
Marks this section as dynamic.
Dynamic sections can contain an undefinded number of completely userdefined options.

#### property .optional = true
Parse optional options

----

## class TypedSection (type, title, description)
An object describing a group of UCI sections selected by their type.<br />
To instantiate use: `Map:section(TypedSection, "type", "title", "description")`
* `type:` UCI section type
* `title:` The title shown in the UI
* `description:` description shown in the UI

#### function :option(optionclass, ...)
Creates a new option
* `optionclass:` a class object of the section
* _additional parameters passed to the constructor of the option class_

#### function :depends(key, value)
Only show this option field if another option `key` is set to `value` in the same section.<br />
If you call this function several times the dependencies will be linked with **"or"**

#### function .filter(self, section) -abstract-
You can override this function to filter certain sections that will not be parsed.
The filter function will be called for every section that should be parsed and returns `nil` for sections that should be filtered.
For all other sections it should return the section name as given in the second parameter.

#### property .addremove = false
Allows the user to remove and recreate the configuration section

#### property .dynamic = false
Marks this section as dynamic.
Dynamic sections can contain an undefinded number of completely userdefined options.

#### property .optional = true
Parse optional options

#### property .anonymous = false
Do not show UCI section names

----

## class Value (option, title, description)
An object describing an option in a section of a UCI File. Creates a standard text field in the formular.<br />
To instantiate use: `NamedSection:option(Value, "option", "title", "description")`<br />
 or `TypedSection:option(Value, "option", "title", "description")`
* `option:` UCI option name
* `title:` The title shown in the UI
* `description:` description shown in the UI

#### function :depends(key, value)
Only show this option field if another option `key` is set to `value` in the same section.<br />
If you call this function several times the dependencies will be linked with **"or"**

#### function :value(key, value)
Convert this text field into a combobox if possible and add a selection option.

#### property .default = nil
The default value

#### property .maxlength = nil
The maximum input length (of chars) of the value

#### property .optional = false
Marks this option as optional, implies `.rmempty = true`

#### property .rmempty = true
Removes this option from the configuration file when the user enters an empty value

#### property .size = nil
The maximum number of chars displayed by form field

----

## class ListValue (option, title, description)
An object describing an option in a section of a UCI File.<br />
Creates a list box or list of radio (for selecting one of many choices) in the formular.<br />
To instantiate use: `NamedSection:option(ListValue, "option", "title", "description")`<br />
or `TypedSection:option(ListValue, "option", "title", "description")`
* `option:` UCI option name
* `title:` The title shown in the UI
* `description:` description shown in the UI

#### function :depends(key, value)
Only show this option field if another option `key` is set to `value` in the same section.<br />
If you call this function several times the dependencies will be linked with **"or"**

#### function :value(key, value)
Adds an entry to the selection list

#### property .widget = "select"
`select` shows a selection list, `radio` shows a list of radio buttons inside form

#### property .default = nil
The default value

#### property .optional = false
Marks this option as optional, implies `.rmempty = true`

#### property .rmempty = true
Removes this option from the configuration file when the user enters an empty value

#### property .size = nil
The size of the form field

----

## class Flag (option, title, description)
An object describing an option with two possible values in a section of a UCI File.<br />
Creates a checkbox field in the formular.<br />
To instantiate use: `NamedSection:option(Flag, "option", "title", "description")`<br />
 or `TypedSection:option(Flag, "option", "title", "description")`
* `option:` UCI option name
* `title:` The title shown in the UI
* `description:` description shown in the UI

#### function :depends (key, value)
Only show this option field if another option `key` is set to `value` in the same section.<br />
If you call this function several times the dependencies will be linked with **"or"**

#### property .default = nil
The default value

#### property .disabled = 0
the value that should be set if the checkbox is unchecked

#### property .enabled = 1
the value that should be set if the checkbox is checked

#### property .optional = false
Marks this option as optional, implies `.rmempty = true`

#### property .rmempty = true
Removes this option from the configuration file when the user enters an empty value

----

## class MultiValue (option, title, description)
An object describing an option in a section of a UCI File.<br />
Creates a list of checkboxed or a multiselectable list as form fields.<br />
To instantiate use: `NamedSection:option(MultiValue, "option", "title", "description")`<br />
 or `TypedSection:option(MultiValue, "option", "title", "description")`
* `option:` UCI option name
* `title:` The title shown in the UI
* `description:` description shown in the UI

#### function :depends (key, value)
Only show this option field if another option `key` is set to `value` in the same section.<br />
If you call this function several times the dependencies will be linked with **"or"**

#### function :value(key, value)
Adds an entry to the list

#### property .widget = "checkbox"
`select` shows a selection list, `checkbox` shows a list of checkboxes inside form

#### property .delimiter = " "
The string which will be used to delimit the values inside stored option

#### property .default = nil
The default value

#### property .optional = false
Marks this option as optional, implies `.rmempty = true`

#### property .rmempty = true
Removes this option from the configuration file when the user enters an empty value

#### property .size = nil
The size of the form field (only used if property `.widget = "select"`)

----

## class StaticList (option, title, description)
Similar to the `MultiValue`, but stores selected Values into a UCI list instead of a character-separated option.

----

## class DynamicList (option, title, description)
A extensible list of user-defined values. Stores Values into a UCI list

----

## class DummyValue (option, title, description)
Creates a readonly text in the form. !It writes no data to UCI!<br />
To instantiate use: `NamedSection:option(DummyValue, "option", "title", "description")`<br />
 or `TypedSection:option(DummyValue, "option", "title", "description")`
* `option:` UCI option name
* `title:` The title shown in the UI
* `description:` description shown in the UI

#### function :depends (key, value)
Only show this option field if another option `key` is set to `value` in the same section.<br />
If you call this function several times the dependencies will be linked with **"or"**

----

## class TextValue (option, title, description)
An object describing a multi-line textbox in a section in a non-UCI form.

----

## class Button (option, title, description)
An object describing a Button in a section in a non-UCI form.
