# Background
In **step3.1**, **step3.2**, **step3.3**, we have seperately introduced the cylc and rose.
To run the UM, we always utilize them together. 
Therefore, in this section we try to elaborate the intersection of cylc&rose.
## [rose suite configuration](https://metomi.github.io/rose/2019.01.8/html/tutorial/rose/suites.html#rose-suite-configurations)
In **step3.3** We have introduced the **rose application**, which is one of the two kind of rose configuration. The other one is the emphasis of this section. It is named rose suite configuration.
### [Configuration Format](https://metomi.github.io/rose/2019.01.8/html/tutorial/rose/suites.html#configuration-format)
A Rose suite configuration is a Cylc suite directory containing a `rose-suite.conf` file.  
Rose Application Configurations are run using rose app-run, Rose Suite Configurations are run using rose suite-run.
###[Running Rose Suite Configurations](https://metomi.github.io/rose/2019.01.8/html/tutorial/rose/suites.html#running-rose-suite-configurations)
Rose Application Configurations are run using `rose app-run`, Rose Suite Configurations are run using `rose suite-run`. Note that the `rose suite-run` doesn't create a work directory in current folder.
When a suite configuration is run:
1. The suite directory is copied into the `cylc-run` directory where it becomes the run directory.
2. Any files defined in the `rose-suite.conf` file are installed.
3. Jinja2 variables defined in the `rose-suite.conf` file are added to the top of the `suite.rc` file.
4. The Cylc suite is validated.
5. The Cylc suite is run.
6. The Cylc GUI is launched.
If you want to make sure everything is right before running the Cylc suite. you can add a command line option `--local-install-only` when you use the `rose suite-run`. This command line option will cause the suite to be installed (though only on your local machine, not on any job hostss) and validated but not run (i.e. steps 1-4).

Like rose app-run, rose suite-run will look for a configuration to run in the current directory. The command can be run from other locations using the -C argument:

`rose suite-run -C /path/to/suite/configuration/`


The name can be overridden using the --name option i.e:

`rose suite-run --name <SUITE_NAME>`
## Glossary
rose suite-run.

`rose suite-log`：
log in the Rose Bush page

