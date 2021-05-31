# Gotchas

Here are some of the lessons learned the hard way, and what can be done about them. 

## Watch the upstream branches.

Quite often there is work in progress upstream that could affect what you're doing, or that you might want to contribute to.  

A good example is the "[all-new-components]()" branch in the [terraform-aws-components]() repo, which is currently active (as of 05-30-2021).

These branches show you a glimmer of how the framework may be changing, and often provide critical insight into how things fit together, which can change over time.


## Always lint your yaml. 

* A good, flexible cli tool is yamllint.

    You should suspect bad yaml *particularly* if you're running into these errors..

    ```
    FOO 
    ```
* Also a gotcha: It is possible to write completely clean yaml, that is STILL wrong.
