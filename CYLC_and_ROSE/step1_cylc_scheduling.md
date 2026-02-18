# **Background:**  
On the PUMA2, the rose and cylc have been well installed. By the **setup** proceeding in `paleoclimate_HadGEM3_UoB/HPC_Accounts/step2`, we can directly invoke the rose and cylc. This description is just for a deeper understanding of the configuration and workflow of rose&cylc.
## Installation  
Note that on the PUMA2, the rose and cylc have been installed already. We can directly use them, without further installation.
Visit: [Installation of rose&cylc](https://metomi.github.io/rose/doc/html/tutorial/rose/index.html)    
## what is cylc?  
Cylc (pronounced silk) is a workflow engine, a system that automatically executes tasks according to schedules and dependencies.
For a detailed guidance, visit: https://cylc.github.io/cylc-doc/7.8.8/html/index.html    
For a quick start, visit: https://metomi.github.io/rose/2019.01.8/html/tutorial/cylc/index.html
### Caveat
This note is for cylc 7. The newest version Cylc 8 differs from Cylc 7 in many ways: architecture, scheduling algorithm, security, UIs, working practices, and more. please refer to [Migration guide from cylc7 to cylc8] (https://cylc.github.io/cylc-doc/stable/html/7-to-8/index.html) for the detail.
## `suite.rc` format
We refer to a Cylc workflow as a **Cylc suite**. A Cylc suite is a directory containing a `suite.rc` file. This configuration file is where we define our workflow. The `suite.rc` file uses a nested INI-based format:
- Comments start with a `#` character.
- Settings are written as `key = value` pairs.
- Settings can be contained within sections.
- Sections are written inside square brackets i.e. `[section-name]`.
- Sections can be nested, by adding an extra square bracket with each level, so a sub--- section would be written `[[sub-section]]`, a sub-sub-section `[[[sub-sub-section]]]`, and so on.
```INI
# Comment
[section]
    key = value
    [[sub-section]]
        another-key = another-value  # Inline comment
        yet-another-key = """
A
Multi-line
String
```
Throughout this tutorial we will refer to settings in the following format:
- `[section]` - refers to the entire section.
- `[section]key` - refers to a setting within the section.
- `[section]key=value` - expresses the value of the setting.
- `[section][sub-section]another-key`. Note we only use one set of square brackets with nested sections.
- 
## Graphing
The graph of a suite refers to the graph strings contained within the [scheduling][dependencies] section. 

### [Graph Strings](https://metomi.github.io/rose/2019.01.8/html/tutorial/cylc/scheduling/graphing.html#graph-strings)

In Cylc we consider workflows in terms of [tasks](https://metomi.github.io/rose/2019.01.8/html/glossary.html#term-task) and [dependencies](https://metomi.github.io/rose/2019.01.8/html/glossary.html#term-dependency). 
[Uploading Graphing — Rose Documentation 2019.01.8 documentation.html…]()

Task are represented as words and dependencies as arrows (`=>`), so the following text defines two tasks where `make_dough` is dependent on `purchase_ingredients`:
```INI
purchase_ingredients => make_dough
```
In a Cylc workflow this would mean that make_dough would only run when purchase_ingredients has succeeded. These dependencies can be chained together:
```INI
purchase_ingredients => make_dough => bake_bread => sell_bread
```
This line of text is referred to as a graph string. These graph strings can be combined to form more complex workflows:
```INI
purchase_ingredients => make_dough => bake_bread => sell_bread
pre_heat_oven => bake_bread
bake_bread => clean_oven
```
Graph strings can also contain “and” (`&`) and “or” (`|`) operators, for instance the following lines are equivalent to the ones just above:
purchase_ingredients => make_dough
pre_heat_oven & make_dough => bake_bread => sell_bread & clean_oven
Collectively these graph strings are referred to as a graph.

### [cylc graphs](https://metomi.github.io/rose/2019.01.8/html/tutorial/cylc/scheduling/graphing.html#cylc-graphs)
In a Cylc suite the graph is stored under the [scheduling][dependencies]graph setting, i.e:
```INI
[scheduling]
    [[dependencies]]
        graph = """
            purchase_ingredients => make_dough
            pre_heat_oven & make_dough => bake_bread => sell_bread & clean_oven
        """
```
This is a minimal Cylc suite. We have not yet provided Cylc with the scripts or binaries to run for each task. This will be covered later in the [runtime tutorial](https://metomi.github.io/rose/2019.01.8/html/tutorial/cylc/runtime/index.html#tutorial-runtime).
Cylc provides a GUI for visualising graphs. It is run on the command line using the `cylc graph <path>` command where the path `path` is to the `suite.rc` file you wish to visualise.

## Basic Cycling
In this section we will look at how to write cycling (repeating) workflows.
### [Repeating Workflows](https://metomi.github.io/rose/2019.01.8/html/tutorial/cylc/scheduling/integer-cycling.html#repeating-workflows)
To make a workflow repeat we must tell Cylc three things:
- The **recurrence**
  How often we want the workflow to repeat.
- The **initial cycle point**
  At what cycle point we want to start the workflow.
- The **final cycle point**
  Optionally we can also tell Cylc what cycle point we want to stop the workflow.

A Bakery example supplied by tutorial:
```diff
[scheduling]
+    cycling mode = integer
+    initial cycle point = 1
    [[dependencies]]
+        [[[P1]]]
            graph = """
                purchase_ingredients => make_dough
                pre_heat_oven & make_dough => bake_bread => sell_bread & clean_oven
            """
```
- The **cycling mode** = integer setting tells Cylc that we want our cycle points to be numbered.
- The **initial cycle point** = 1 setting tells Cylc to start counting from 1.
- P1 is the **recurrence**. The graph within the [[[P1]]] section will be repeated at each cycle point.

### [Inter-Cycle Dependencies]（https://metomi.github.io/rose/2019.01.8/html/tutorial/cylc/scheduling/integer-cycling.html#inter-cycle-dependencies）
In the last session, we introduced the basic cycling syntax—placing the graph inside the [[[P1]]] section. In real workflows, dependencies between different cycles are common. To support this, Cylc provides a set of syntax features specifically designed to represent inter-cycle dependencies.

By default recurrences start at the initial cycle point, however it is possible to make them start at an arbitrary cycle point. This is done by writing the cycle point and the recurrence separated by a forward slash (`/`), e.g. `5/P3` means repeat every third cycle starting from cycle number 5.

The start point of a recurrence can also be defined as an offset from the initial cycle point, e.g. `+P5/P3` means repeat every third cycle starting `5` cycles after the initial cycle point.

Here comes a example in the [practical] (https://metomi.github.io/rose/2019.01.8/html/tutorial/cylc/scheduling/integer-cycling.html#admonition-5)

## [Date-Time Cycling](https://metomi.github.io/rose/2019.01.8/html/tutorial/cylc/scheduling/datetime-cycling.html#date-time-cycling)
In last session, we introduced the cycling mode of integer and the two corresponding ingradient: **initial cycle point** and **recurrence**. They are useful to show the configuration of the cylc. However, in the real work flow of tasks like climate simulations, the cycling is always in the form of date-time. 
In Cylc, dates, times and durations are written using the ISO 8601 format - an international staandard for representing dates and times. please refer to [ISO8601](https://metomi.github.io/rose/2019.01.8/html/tutorial/cylc/scheduling/datetime-cycling.html#iso8601) for further details.
### Date-Time Recurrences
In integer cycling, suites’ recurrences are written P1, P2, etc.
In date-time cycling there are two ways to write recurrences:
1. Using ISO8601 durations (e.g. P1D, PT1H).
2. Using ISO8601 date-times () with inferred recurrence.

A recurrence can be inferred from a date-time by omitting digits from the front. For example, if the year is omitted then the recurrence can be inferred to be annual. E.G:
```
2000-01-01T00   # Datetime - midnight on the 1st of January 2000.

     01-01T00   # Every year on the 1st of January.
        01T00   # Every month on the first of the month.
          T00   # Every day at midnight.
          T-00  # Every hour at zero minutes past (every hour on the hour).
```
#### The Initial & Final Cycle Points
There are two special recurrences for the initial and final cycle points:
- R1: repeat once at the initial cycle point.
- R1/P0Y: repeat once at the final cycle point.
#### [Inter-Cycle Dependencies](https://metomi.github.io/rose/2019.01.8/html/tutorial/cylc/scheduling/datetime-cycling.html#inter-cycle-dependencies)
Inter-cycle dependencies are written as ISO8601 durations, e.g:
- `foo[-P1D]`: the task foo from the cycle one day before.
- `bar[-PT1H30M]`: the task bar from the cycle 1 hour 30 minutes before.
- The `initial cycle point` can be referenced using a caret character `^`, e.g:
`baz[^]`: the task `baz` from the initial cycle point.
### example from [practical](https://metomi.github.io/rose/2019.01.8/html/tutorial/cylc/scheduling/datetime-cycling.html#admonition-8)
```INI
[cylc]
    UTC mode = True
    # this flag means the
[scheduling]
    initial cycle point = 20000101T00Z
    [[dependencies]]
        [[[R1]]]
            graph = """
                build_adergrove_station
                build_camborne_station
                build_heathrow_station
                build_shetland_station
            """
        [[[PT3H]]]
            graph = """
                build_adergrove_station[^] => get_observations_aldergrove => consolidate_observations
                build_camborne_station[^] => get_observations_camborne => consolidate_observations
                build_heathrow_station[^] => get_observations_heathrow => consolidate_observations
                build_shetland_station[^] => get_observations_shetland => consolidate_observations
            """
        [[[+PT6H/PT6H]]]
            graph = """
                consolidate_observations => forecast
                consolidate_observations[-PT3H] => forecast
                consolidate_observations[-PT6H] => forecast
                get_rainfall => forecast => post_process_exeter
            """
```
## Further Scheduling    
### Qualifiers    
dependencies like `foo => ba`r is, in fact, shorthand for `foo:succeed => bar`.
We refer to the `:succeed` descriptor as a qualifier. There are qualifiers for different task states e.g:
- `:start`
When the task has started running.
- `:fail`
When the task finishes if it fails (produces non-zero return code).
- `:finish`
When the task has completed (either succeeded or failed).
### Clock Triggers    
To be supplemented in the future.
### Alternative Calendars  
To be supplemented in the future.
## 
## Glossary
- cylc graph <path>
used to display a diagram of a suite.rc.



