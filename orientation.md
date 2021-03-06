# Cloudposse: A Guided Tour

> Hey all --<br> 
> I've been having great fun of late playing with the Cloudposse tool set which you have so generously made available. It is a sophisticated and inspired framework for spinning up a breathtaking variety of infrastructure at scale. I've been impressed by the number of labor-saving features it seems to layer together.  I'm hoping that these notes might help others orient themselves. As I am still a rank novice, please feel free to point out any errors in my assumptions. I feel lucky to have discovered this system at such an early moment in its developmental life cycle.  Looking forward to great things! <br> 
>       -- mslayton 2021-04-28<br>

# Orientation

These notes were created during my initial spin up on the Cloudposse framework of tools and techniques in early 2021.  They assume you have a basic understanding of the common tools in use by devops professionals, such as terraform and Docker, and provides tutorial links to some of the newer tools introduced by the framework, such as variant2.  Please note that these tools may have evolved since the writing, and that I make no guarantees as to the accuracy of this document moving forward.

The framework makes heavy use of [Docker](https://docker-curriculum.com/), and 
[GNU make](https://www.gnu.org/software/make/manual/make.html), often as a way to remove the complexity of installing and maintaining state across [dozens of tools](./tools.md) which may or may not be familiar to every potential user. 

# The Dependency Inversion Principle 

The Cloudposse Framework is rooted in the [Principle of Dependency Inversion](https://en.wikipedia.org/wiki/Dependency_inversion_principle).  In straightforward terms, it tries to improve the loose coupling of components by separating code from configuration wherever possible, which helps to greatly streamline the process of martialing infrastructure. It capitalizes on the fact that many, many code modules for infrastructure resources [have already been written](https://github.com/cloudposse/terraform-aws-components), and tries to get people configuring infrastructure long before they have to sit down and write their own terraform modules (although it also provides guidance in that case).  

In principle, Dependency Inversion tends to make existing components more reusable, as it stresses that lower level modules do not depend on higher level modules.  Instead, modules must depend upon interfaces for exchanging configuration data. This facilitates the flexible composition of larger infrastructures from lower-level reusable components. Dependency Inversion [has also been talked about in the terraform community](https://www.terraform.io/docs/language/modules/develop/composition.html#dependency-inversion).  It emphasizes the idea that components should be loosely coupled, meaning that architectures are built out of layers of indpendent components, each handling their individual concerns separately, and providing well defined boundaries to share configuration details with other components. 

## Examples from terraform
Separation of configuration and code can be achieved in more modern versions of terraform (0.13 and above) by passing variables into a module that are not just strings, numbers, or lists, but rather large maps of configuration data, nested many layers deep. 

In fact, it is possible to describe an entire configuration for something as intricate as an RDS cluster, or a VPN, by simply passing in that configuration as a single nested variable.  Because the data being passed into the module is complex in structure, the process of building the data structure is referred to as "[deep merging](https://registry.terraform.io/modules/cloudposse/stack-config/yaml/0.1.0/submodules/deepmerge)".  Why is this important?  Because yaml has [certain limitations](https://stackoverflow.com/questions/41620674/use-placeholders-in-yaml) on its own that prevent us from building very DRY configurations. Thus, we often build  larger data structures by merging many smaller ones. More on this in a moment, when we talk about "modules", "components", and "stacks". 

Passing in nested data allows us to restructure modules so that they expect this kind of configuration, and such that they require very little else.  [Many of the Cloudposse terraform modules](https://github.com/cloudposse/terraform-aws-components) have already been restructured to accomodate this principle, and still more are being written every day.  

## Anatomy of a cloudposse module
To accomodate the passing in of structured data, let's take a look at [a specific example](https://github.com/cloudposse/terraform-yaml-stack-config/tree/main/modules/env). The 'terraform-yaml-stack-config' is a basic blueprint for a Cloudposse module that anyone can use to create a pluggable piece of infrastructure. 

Notice that there are a [handful of helper sub-modules](https://github.com/cloudposse/terraform-yaml-stack-config/tree/main/modules) under the modules directory in this Github repository.  These helper sub-modules implement a general pattern that helps define how data gets passed into and out of any module that makes use of  the cloudposse framework. When you build a new module, you do it in a module template that includes these submodules by default.  Let's take a look at one particular helper module, to see what is happening: 


https://github.com/cloudposse/terraform-yaml-stack-config/tree/main/modules/vars

The 'vars' submodule is a helper module which defines the structure of nested data that is to be passed into your module, and is returned in the output of your module.  NOTE: In this case, the module that we are building has all of these helper modules predefined as part of its scaffolding. You do not have to write these helper modules, you merely include them as part of your own infrastructure module. 

Also note that modules return deeply nested data structures as **outputs** as well as accepting them as inputs. This is so their configuration can be passed as a general pattern to other modules, and other parts of the infrastructure that may require this data.  This general pattern, shared by all modules, helps to achieve the goal of loose coupling. 

# The passing of "context" data

```  module "vars" {
    source = "cloudposse/stack-config/yaml//modules/vars"
    # version     = "x.x.x"
  
    stack_config_local_path = "./stacks"
    stack                   = "my-stack"
    component_type          = "terraform"
    component               = "my-vpc"
  
    context = module.this.context
  }
```

Notice the variable called "context".  It is exposed from an internal variable called "this.context", which contains the nested data structure that encapulates all your module's configuration. It also includes some special data structures, such as a component tag -- a standardized way to define each resource spun up in the cloud as part of your infrastructure. 

There are two other important variables here:  One called "stack" and another called "component".  We'll talk about these next.

# Modules are organized into Components

In addition to modules you define for your own infrastructure, there is also a larger perspective of how modules fit together into entire architectures. There are lots of issues that can thwart how we organize our infrastructure, and so Cloudposse tries to make it easier to combine modules together into a coherent system. The first part of that system is how Modules can be combined to form Components.

A component, in this sense, is a collection of independent modules all of which can pass around a nested hierarchy of their collected context. A component provides two important functions: First, there's a bit more loose coupling, in that each module does not need to predefine a specific remote resource in order to pass its configuration to another module within the same component. Modules within a component can pass all their configuration data freely.  In order to retrieve the proper context from another module, you simply query the component context, which is the parent to both modules, and ask for one of the other sibling's nested attributes as part of the component's context.

This enforces loose coupling standards and reduces the work required to manage and maintain a large infrastructure, which might contain many subsystems organized into many independent provider accounts, or subdomains.

# How is a component better?
  
Do components make your design more loosely coupled? 
How is a component better than just a bunch of modules?

These are good questions.  Consider how the same situation might work with two terraform modules that use independent tfstates.  

<diagram> 
    
Example:

This case comes up quite a lot in terraform design. For example you have a VPC module, Module A, and a module that provides dynamic subnets, Module B. VPC modules do not change all that often, but occasionally, one might want to add or even restructure the subnets in Module B. 

In this case, if you want module B to get state information from module A, the typical way to do it might be to define a terraform_remote_state data source in module A, which then recovers a few scraps of information from module B. 

This works rather well, however, you must also modify the outputs in Module B on order to provide the necessary info.  

What if module B was written by somebody else?  What if you're really not all that familiar with what Module B does?  This kind of tight coupling -- that is, getting information about module B requires actively changing module B -- is at the heart of a great deal of needless effort in remote state management.  

It is far easier to simply check the nested structure provided by both modules to find the needed data for Module A. Using the Principle of Dependency Inversion, there is no need to rewrite Module B.  It already provides all its info to the component's helper modules in the form of a nested configuration variable called "context".  By reading the context provided by a component's helper modules, Module A can get all the information it needs without the need to change Module B. 

This is an important point:  The contexts are nested; First by module, then by component, and finally by stack.   

# A note about Ordering, and Independent execution

Before we get too much deeper into the subject of organizing modules into components, and components into stacks, it's worth taking a second to talk about another aspect of loose coupling -- independent execution.  Components and their module constituents are generally managed as a single contiguous tfstate. That is to say, when you build a component, you update it via a single terraform run.  Any state that the component manages is recorded as a single, independent tfstate file, written to the s3 bucket it uses for managing its own state. 

# Components are organized into Stacks

By contrast, stacks are composed of multiple components. Hence, it may take several terraform runs, one per component, and in a particular order, to achieve the build out of an entire stack.

A typical component might be a VPC, an ElasticSearch cluster, or an RDS Cluster.  By contrast, a typical stack might define all the systems for an entire AWS account.  If a stack defines, for example, an entire AWS account, then it might well include first a VPC definition (hence, an independent tfstate), then an RDS cluster definition, and finally, let's say, an application cluster of some sort. 

Each of these would be defined as a separate component, with a superimposed ordering:  To build a this stack, you must first have a VPC, and then an RDS Cluster, before you can finally spin up the application cluster.  The ordering is important here, as well as the three independent Terraform runs required to complete the stack. 

# Wrappers, the key to ordered execution

How does Cloudposse achieve this independent ordering?  In short, it's done through the use of sophisticated wrapper tools, which can run terraform multiple times, using different configuration sets per run, and in a specific order (or even with parallel ordering, in some cases).  This is the world of wrappers such as Atmos, which we will visit shortly, and the extraordinary library for defining these wrapper workflows, known as [Variant2](https://github.com/mumoshu/variant2/).

# Geodesic - The handy toolbox

Before we dive into the implementation of all these components into a self-contained, intuitive suite of tools, let's talk for a second about the central organizing framework for these tools, known as Geodesic. 


# [Atmos](https://github.com/cloudposse/atmos) - cloudposse's default wrapper

The Atmos README.md has a great write up of how to use the wrapper tool, and how to build a series of components into a servicable infrastructure.  It is missing one important piece, which is a few components in the components dir. Otherwise, it is complete.  

The Atmos README.md also walks through the framework in detail, starting with tools like Geodesic and the [GNU make](https://www.gnu.org/software/make/manual/make.html) based [Build Harness](https://github.com/cloudposse/build-harness).

(RE: Build Harness -- IMHO we could use a quick tutorial in using the many nested Makefiles (make help), and modules under .build_harness/modules/.  I will add this to the list. 

# [Variant2](https://github.com/mumoshu/variant2/) - the wrapper library

IMHO, it would be useful to have a procedure for adding a small change to the Atmos wrapper, and pushing it live. 

Here is my best approximation: 


# The anatomy of a wrapper 
## Atmos components 
## [Vendir](https://carvel.dev/vendir/docs/latest/sync/)
## [Syncing with Carvel](https://carvel.dev/vendir/docs/latest/sync/)
## [GoReleaser](https://goreleaser.com/quick-start/)
Calling all C++ gurus -- a missing piece, when it comes to caching... 

# Building custom wrappers, with custom workflows.

Let's start with [Atmos](https://github.com/cloudposse/atmos).
Defining workflows in yaml.

# Putting it all together

# Other Tools
## How docker containers help organize.
## The prevalent use of GNU make
## [Build-harness](https://github.com/cloudposse/build-harness) - reusable make routines

# managing roles (and secrets) securely
## aws-vault
## aws-okta
## aws2saml
## Chamber

# Creating your own [package repository](https://github.com/cloudposse/packages)
## Creating an [alpine package](https://wiki.alpinelinux.org/wiki/Creating_an_Alpine_package).
## [Managing an Alpine package repository](https://wiki.alpinelinux.org/wiki/Alpine_Linux_package_management)
## Creating a Cloudpossee [mini-release](https://github.com/gangofnuns/atmos/releases/tag/v0.17.2-beta.1)

# BUILDING a multi-account architecture

# Organizing (and building) a large reference architecture
(Should be its own tree of documents.)

# Reference
## GUIDE to importing packages into the Cloudposse framework.
## How to contribute.
## Where to find help.


