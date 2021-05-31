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
### PRO TIPS 
* Watch the upstream commits.
* Ask informed questions. 
* Gotchas - hard lessons learned.
    * workspace overwrites - these will blow away your state. 
    * version mismatches - use upstream only.  watch upstream for bugs reported.
    * Always lint your yaml files. 

                    * account state issues - 
                * Converting older modules - a guide
                    * You will inevitably have to convert something old into something new. 
                * Troubleshooting - how to break things down when there are problems. 
