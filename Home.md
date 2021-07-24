Welcome to the axiom wiki!

Axiom is a dynamic infrastructure framework for *everybody*! What does this mean? Well, axiom is kind of unique but is just an implementation of many common cloud technologies. 

Axiom is a framework that allows you to easily orchestrate one, or many cloud instances to distribute the processing of a task or many tasks. There are also numerous helper utilities that make moving files between your instances, backing up your instances, and initializing and deleting one or many instances a painless task. 

When making axiom, I wanted to make something that followed the Unix philosophy closely, to create the building blocks for people to create easy and predictable continuous scanning pipelines or general one-off highly parallelized workloads. 

Most of the usage of this project has occurred in the red-team & bug bounty spaces, mostly because it makes it very easy to predict your spending on instances and remove some of the mystery and complications behind using cloud instances to remotely perform tasks.

This is *not* a competitor of things like Terraform and Ansible, they're both fantastic technologies and you should definitely use them! Tools like Terraform, however, do not lend themselves to being easily modifiable on the fly. For infrastructure, application deployment and general management it is fantastic. For splitting an input dynamically and scaling to process the input, axiom is king (in my opinion :D).

Please refer to the sidebar on the right, there is a quick run-down of different ways to use axiom. 