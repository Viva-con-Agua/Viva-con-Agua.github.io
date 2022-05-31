# DevOps

> DevOps is a set of practices that combines software development (Dev) and IT operations (Ops). 
> It aims to shorten the systems development life cycle and provide continuous delivery with high software quality.

From this definition, three key principles are often derived: *Shared Ownership, Workflow Automation* and *Rapid Feedback*.
In this document, I will explain how VcA implements these principles on an abstract level.
For more specific details see the linked articles in the following sections or explore the sidebar.

## Shared Ownership

One of the key principles of *DevOps* is that there exists a shared ownership of the problem domain between development and operations department.
At VcA we currently only have one IT team so this part is naturally implemented…

Generally we use specific services to solve small parts of our problems i.e. we use microservices.
For more details visit the [Software Architecture](/devops/software_architecture) article.

## Workflow Automation

Our workflows are currently entirely non-automated, but we are working on it.
In preparation for proper CI/CD processes we are however already doing the following:

- All new services are built and deployed using **containers**.
  While deployment is not yet done automatically, with containers we can easily build and deploy a service manually.

## Rapid Feedback

Our feedback cycles are generally not too long since we test our services ourselves and generally have good connections to other departments that give us feedback.
This however in no way counts as *rapid feedback* and we currently do not have an established testing / linting / … methodology.
