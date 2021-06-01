# The mother of all projects
This rather large project incorporates many (if not most) of the techniques discussed elsewhere 
in these tutorials. The goal is ambitious: Produce a kind of "master class" describing how to 
build a full-blown, multi-account architecture, at scale, using the SweetOps Framework tools 
and best practices. From a high level, any big project should be split into phases for easy 
navigation. Here's a quick overview. 


# prep work

## Gathering the right components

* Introduction to vendir
* Using a vendor file to gather all the proper components. 

## General ramp up

* What you should know before you begin, and where to read about it. 

* You should know about [Geodesic](https://github.com/cloudposse/geodesic), and how to use it as a basic tool
as part of this tutorial.  

* You should know about [Atmos](https://github.com/cloudposse/atmos), and how to use it as a wrapper when running
terraform in the Geodesic environment.

    * For Geodesic and Atmos above, you should go through [these 3 online tutorials](https://github.com/cloudposse/tutorials).
    
* You should know about the 'context' pattern. 
    * Read this [orientation](orientation.md) doc, which discusses some of the SweetOps design.
    
* You need to know about '[deep merging](deep_merging.md)'.
    * Read this [tutorial](projects/deep_merging.md).

## PHASE 1 - configure the [tfstate-backend]() component, using DRY configs.

It's important to set up a viable tfstate-backend config, so let's do this 
first.  It will also be useful to create multiple components from a single 
templated example. Deep merging will then allow us to replicate these components 
across multiple accounts and stacks using DRY configurations. A few nifty
tips and tricks are added, to make things easier.

### GOALS
* Create a scratch multi-account environmemnt using AWS Organizations, 
Organizational Units (OU's), and policies.

* Build a [terraform-aws-tfstate-backend]() component in the master account, 
using the latest [terraform-aws-yaml-config]() modules. 

* Configure [deep-merging]() such that you can spin up multiple acccounts, each with
their own unique [tfstate-backend]() and distinctive config, but DRY to the point 
that all configs use a single code module.


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


