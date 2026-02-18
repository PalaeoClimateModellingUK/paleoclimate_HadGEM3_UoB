## Runtime Configuration
the content of this file  are mostly taken from [Runtime Configuration session](https://metomi.github.io/rose/2019.01.8/html/tutorial/cylc/runtime/introduction.html#introduction) and [introduction session](https://metomi.github.io/rose/2019.01.8/html/tutorial/cylc/runtime/introduction.html#introduction) of cylc tutorial.    

So far, we have been working with  `[schedule]` section. This is where the workflow is defined in terms of tasks and dependencies.  

In order to make the workflow runnable 
we must **associate tasks with scripts or binaries** to be executed when the task runs. 
This means working with the `[runtime]` section which determines what runs, as well as where and how it runs.

### [The script Setting](https://metomi.github.io/rose/2019.01.8/html/tutorial/cylc/runtime/introduction.html#the-script-setting)
We tell Cylc what to execute when a task is run using the `script` setting. To avoid make this note a simple repetition of the rutorial, 
I will introduce the runtime sector and its configuration by the instance in the [pratical of the tutorial](https://metomi.github.io/rose/2019.01.8/html/tutorial/cylc/runtime/introduction.html#admonition-3).
```INI
[cylc]
    UTC mode = True
[scheduling]
    initial cycle point = 2000-01-01T00Z
    final cycle point = 2000-01-01T06Z
    [[dependencies]]
        [[[T00/PT3H]]]
            graph = """
                get_observations_camborne => consolidate_observations
                get_observations_heathrow => consolidate_observations
                get_observations_shetland => consolidate_observations
                get_observations_belmullet => consolidate_observations
            """
        [[[T06/PT6H]]]
            graph = """
                consolidate_observations => forecast
                consolidate_observations[-PT3H] => forecast
                consolidate_observations[-PT6H] => forecast
                get_rainfall => forecast
                forecast => process_exeter
            """
        [[[T12/PT6H]]]
            graph = """
                forecast[-PT6H] => forecast
            """

[runtime]
    [[get_observations_belmullet]]
        script = get-observations

    [[get_observations_camborne]]
        script = get-observations

    [[get_observations_heathrow]]
        script = get-observations

    [[get_observations_shetland]]
        script = get-observations

    [[consolidate_observations]]
        script = sleep 3;

    [[forecast]]
        script = echo 'Running forecast.'; sleep 5

    [[get_rainfall]]
        script = cat >rainfall <<<'The rain in Spain falls mainly on the plain'

    [[process<site>]]
        script = """python3 -c "
            from random import choice
            print choice(['Windy', 'Rainy', 'Sunny', 'Snowy'])"
        """
```
In the `[runtime]` section of this suite.rc, the tasks specified in `[scheduling]` are linked to their actual commands by defining the `script`.
Note that in the `[scheduling]` section, tasks are arranged within the **graph** under the `[[[recurrence]]]` sections, whereas in `[runtime]` each task is denoted in the sub-section of `[runtime]`.
Among the runtime tasks, the `process<site>` is a special one, because there is no identical identical task can be found in scheduling.  

The task `process<site>` in the `[runtime]` section is a **template task** — it's not a task that runs by itself. Instead, it's used to define common settings (like the script or environment) for a family of tasks that follow the same structure but vary by name.
In the `[scheduling]` section, we see an instance of this template used:
```ini
forecast => process_exeter
```
Here, `process_exeter` is a concrete task instance derived from the `process<site>` template. Cylc automatically recognizes that process_exeter matches the template and applies the corresponding runtime configuration from `process<site>`.

### Further configuration
In addition to link the command to tasks, There are some configurations for certain tasks claimed in the [runtime] section.  

For example, we can specify environment variables in a task’s [environment] section. These [[[environment]]] variables are then provided to **jobs**(jobs is the instances of tasks) when they run.
```ini
[runtime]
    [[countdown]]
        script = seq $START_NUMBER
        [[[environment]]]
            START_NUMBER = 5
```
Each job is also provided with some standard environment variables e.g:  
- CYLC_SUITE_RUN_DIR:
The path to the suite’s run directory (e.g. ~/cylc-run/suite).
- CYLC_TASK_WORK_DIR:
The path to the associated task’s work directory (e.g. run-directory/work/cycle/task).
- CYLC_TASK_CYCLE_POINT:
The cycle point for the associated task (e.g. 20171009T0950).

There are some other configurations about the submission of **jobs**, timeouts, and Retries.
## About the Runing of cylc
As we can see, The suite.rc are more or less repetitive or redundant.  
Cylc offers three ways of consolidating configurations to help improve the structure of a suite and avoid duplication.
- [Families](https://metomi.github.io/rose/2019.01.8/html/tutorial/cylc/runtime/configuration-consolidation/families.html) work best consolidating runtime configuration by collecting tasks into broad groups, e.g. groups of tasks which run on a particular machine or groups of tasks belonging to a particular system.
- [Jinja2](https://metomi.github.io/rose/2019.01.8/html/tutorial/cylc/runtime/configuration-consolidation/jinja2.html)  is good at configuring settings which apply to the entire suite rather than just a single task, as we can define variables then use them throughout the suite.
- [Parameterised Tasks](https://metomi.github.io/rose/2019.01.8/html/tutorial/cylc/runtime/configuration-consolidation/parameters.html) works best for describing tasks which are very similar but which have subtly different configurations (e.g. different arguments or environment variables).
## About the Runing of cylc
visit the links below For detailed informations about:
- [relationship bettween tasks and jobs](https://metomi.github.io/rose/2019.01.8/html/tutorial/cylc/runtime/introduction.html#tasks-and-jobs)
- [how to run the suite](https://metomi.github.io/rose/2019.01.8/html/tutorial/cylc/runtime/introduction.html#running-a-suite)
- [where do the output generate](https://metomi.github.io/rose/2019.01.8/html/tutorial/cylc/runtime/introduction.html#where-do-all-the-files-go)
- [The Cylc GUI](https://metomi.github.io/rose/2019.01.8/html/tutorial/cylc/runtime/introduction.html#the-cylc-gui)
## Glossary
- `cylc validate <path/to/suite>`
a command which automatically checks for any obvious configuration issues
- `cylc run <name>`
run the suite
- `cylc reg <name> <path/to/suite>`
register a workflow suite before running it, not necessary.
- `cylc gscan`
Scans and lists all currently running suites for the current user.
- `cylc help all`
lists all the available command of cylc
- `cold start`
A cold start is one in which the suite starts from the initial cycle point. This is the default behaviour of cylc run.
- `warm start`
In a cycling suite a warm start is one in which the suite starts from a cycle point after the initial cycle point. Tasks in cycles before this point as assumed to have succeeded.
[A example to use cold and warm start](https://code.metoffice.gov.uk/trac/moci/wiki/ModelRestartability#Atmospherejobs-forcingaCRUN)
