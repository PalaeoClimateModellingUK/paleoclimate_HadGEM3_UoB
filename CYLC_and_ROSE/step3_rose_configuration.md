## what is rose?
Rose is a toolkit for writing, editing and running application configurations.
Rose also contains other optional tools for:

- Version control.

- Suite discovery and management.

- Validating and transforming Rose configurations.

- Interfacing with Cylc.  

Visit: [rose tutorial](https://metomi.github.io/rose/2019.01.8/html/tutorial/rose/index.html)

## Rose Configuration
Rose configurations are **directories** containing a Rose configuration file （`rose-*.conf`） along with other optional assets which define behaviours such as:
- Execution.
- File installation.
- Environment variables.

Rose configurations may be used standalone or alternatively in combination with the Cylc workflow engine. There are two types of Rose configuration for use with Cylc:

- Rose application configuration
A runnable Rose configuration which executes a defined command.

- Rose suite configuration
A Rose configuration designed to run Cylc workflows. For instance it may be used to define Jinja2 variables for use in the `flow.cylc` file.

### Rose Configuration Format
All Rose configuration files use the same format which is based on the INI file format. Like the file format for Cylc workflows:
- Comments start with a `#` character.
- Settings are written as `key=value` pairs.
- Sections are written inside square brackets i.e. `[section-name]`    
However, there are also key differences, and unlike the file format for Cylc workflows:
- Sections cannot be nested.
- Settings should not be indented.
- Comments must start on a new line (i.e. you cannot have inline comments).
- There should not be spaces around the `=` operator in a `key=value` pair.
For example:
```INI
# Comment.
setting=value

[section]
key=value
multi-line-setting=multi
                  =line
                  =value
```
Throughout this tutorial we will refer to settings in the following format:
- `file` - will refer to a Rose configuration file.
- `file|setting` - will refer to a setting in a Rose configuration file.
- `file[section]` - will refer to a section in a Rose configuration file.
- `file[section]setting` - will refer to a setting in a section in a Rose configuration file.

### Why use rose configuration
With Rose configurations the **inputs** and **environment** required for a particular purpose can be encapsulated in a simple **human-readable** configuration.
Configuration settings can have **metadata** associated with them which may be used for multiple purposes including automatic checking and transforming.    
Rose configurations can be edited either using a text editor or with the `rose config-edit` (rose config-edit filename.conf) GUI which makes use of metadata for display and on-the-fly validation purposes.    

### Rose Applications
A Rose application or “Rose app” is a runnable Rose configuration which executes a defined command.
Rose applications provide a convenient way to encapsulate all of this configuration, storing it all in one place to make it easier to handle and maintain.    
the configuration of Rose app (application configuration) is a diectory containing a `rose-app.conf` file.     
Try to reproduce the example on [Rose Applications tutorial](https://metomi.github.io/rose/doc/html/tutorial/rose/applications.html) for a deeper understanding.    

To run a Rose application, navigate to the directory containing the rose-app.conf file and run the following command：
```bash
rose app-run -q  # -q for quiet output
```
But the more recomanded way is to add a **run** folder then run the application in it:
```bash
mkdir run
cd run
rose app-run -q -C ../  # -C option to provide the path to the application
```
## Rose Metadata
Metadata can be used to provide information about settings in Rose configurations.

It is used for:
- Documenting settings.
- Performing automatic checking (e.g. type checking).
- Formatting the rose config-edit GUI.
Metadata can be used to ensure that configurations are valid before they are run and to assist those who edit the configurations.
### The Metadata Format
The metadata of a configuration is written in a  `rose-meta.conf` which uses the standard rose configuration format. This file can either be stored inside a Rose configuration in a meta/ directory, or elsewhere outside of the configuration.
The metadata for a setting is written in a section named `[section=setting]` where setting is the name of the `setting` and `section` is the section to which the setting belongs (left blank if the setting does not belong to a section).
For example, below is a rose-meta of a 'hello world' app:
```bash
[env=WORLD]
description=The name of the world to say hello to.
values=Mercury, Venus, Earth, Mars, Jupiter,
      =Saturn, Uranus, Neptune
```
This example gives the `WORLD` variable a title and a list of allowed `values`.
Try to reproduce the example on [Pratical about editting metadata](https://metomi.github.io/rose/doc/html/tutorial/rose/metadata.html) for a deeper understanding.    

### [Rose Configuration Format](https://metomi.github.io/rose/doc/html/api/configuration/rose-configuration-format.html#rose-configuration-format)
Further detail for the syntax of rose.
Such as the usage of `!` and the setting of [optional-configuration for apps](https://metomi.github.io/rose/2.3.0/html/api/configuration/rose-configuration-format.html?utm_source=chatgpt.com#optional-configuration)


## Rose Glossary
- rose config-edit
- rose app-run
- rose metadata-check








