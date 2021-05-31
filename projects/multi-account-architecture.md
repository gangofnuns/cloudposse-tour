# The mother of all projects
This extensive project incorporates many (if not most) of the techniques discussed elsewhere in these tutorials. The idea is ambitious: Produce a kind of "master class" in how to produce a full-blown, multi-account architecture, at scale, using the SweetOps Framework of tools and best practices.


# HIGH-LEVEL OVERVIEW
Any big project should be split into phases and navigable. Here's a quick overview. 

# Gather necessary components. 

## prep work

### Gather components

* Understanding vendir

### General ramp up
* What to read. 
* You need to know about Geodesic, and Atmos.
    * Go through the 3 online tutorials.
* You need to know about the 'context' pattern. 
    * Read this [orientation](orientation.md).
* You need to know about '[deep merging](deep_merging.md)'.
    * Read this [tutorial](projects/deep_merging.md).

### STEP 1 - configure the [tfstate-backend]() component
 * It's important to set up a viable tfstate-backend config, so we'll do this first. It will also be useful for creating multiple components from a single templated example that are reused across stacks. 

 ### STEP 2 - configure [IAM permissions]()
* Next, you will need to know about IAM permissions, saml-based SSO, and role assumption.
* Important paradigms in use
    * deep merging - the importance of having a DRY config.
    * context - how it fits into the picture. 

    * Phase I - Building a multi-account framework
        * Start with a multi-account in AWS.  (tips on how to do this right, with emails, etc.)
        * Spin up tfstate-backend in master
            * (use deep merging)
            * Caveats - DO NOT use older remote-state files.  Use th vendir file approach.)
    * Phase II - Creating SSO integration 
        * Important concepts - role assumption, tagging, etc.  Okta groups mapping to roles.
        * How to structure things. 
      IAM Permissions, etc. 
        * Using the Okta provider.
        * Using aws2saml. 


    * Phase III - 
                    



# Understanding Vendir


Use the following [vendir.yaml](https://github.com/gangofnuns/vendir.yaml).


