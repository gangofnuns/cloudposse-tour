# The mother of all projects
This rather large project incorporates many (if not most) of the techniques discussed elsewhere in these tutorials. The goal is ambitious: Produce a kind of "master class" describing how to build a full-blown, multi-account architecture, at scale, using the SweetOps Framework tools and best practices.


# From a high level
Any big project should be split into phases and navigable. Here's a quick overview. 


# prep work

## Gathering the right components

* Introduction to vendir
* Using a vendor file to gather all the proper components. 

## General ramp up

* What to read. 
* You need to know about Geodesic, and Atmos.
    * Go through the 3 online tutorials.
* You need to know about the 'context' pattern. 
    * Read this [orientation](orientation.md).
* You need to know about '[deep merging](deep_merging.md)'.
    * Read this [tutorial](projects/deep_merging.md).

## PHASE 1 - configure the [tfstate-backend]() component

It's important to set up a viable tfstate-backend config, so let's do this 
first.  It will also be useful to create multiple components from a single 
templated example. Deep merging will then allow us to replicate these components 
across multiple accounts and stacks using only DRY configurations. 

GOALS: 
## PHASE 2 - configure [IAM permissions]()
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


