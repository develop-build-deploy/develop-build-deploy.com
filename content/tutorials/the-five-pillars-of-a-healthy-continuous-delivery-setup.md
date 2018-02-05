---
date: 2018-01-17T16:13:00+01:00
lastmod: 2018-01-17T16:13:00+01:00
title: The Five Pillars of a healthy Continuous Delivery setup
authors: ["manuelkiessling"]
tags:
  - authors
slug: the-five-pillars-of-a-healthy-continuous-delivery-setup
---

## Introduction

The goal of this project is to teach you how to build successful Continuous Delivery setups for your software products. The goal of this post is to create a shared understanding of the different practices and principles that together are the foundation for Continuous Delivery setups that are stable, maintainable, and adaptable.

I call these *the Five Pillars of a healthy Continuous Delivery setup*.

These pillars are:

* Infrastructure-as-Code
* Immutable Servers
* Zero-Downtime Blue-Green Deployment
* Software Tests
* Software-based database Migrations

This is not an ordered list. All five practices and principles are equally important for a successful setup, and they reinforce each other to form a working whole.

Let's have a closer look at each:


## Infrastructure-as-Code

Running a Continuous Delivery setup successfully always means running systems infrastructures (server systems, network rules, DNS entries, firewall settings, logging setups etc.) that is completely under your control, with no surprises lurking in dark corners.

Why? Because at the end of the day, system infrastructures are the playing field on which the Continuous Delivery game is played. And it is only played well if the playing field is kept in excellent condition.

You will use parts of these infrastructures to execute the Continuous Delivery setup itself, and you will use other parts as the delivery goal of your setup, the place where your applications and services will run.

If you don't have full control over these, you are out of the game: you will not be able to reliably deliver new functionality again and again with confidence if servers don't look the way you expect, if firewall rules are not in place as needed, or if DNS entries are a mess. 

How do you achieve this state of running and managing well-maintained and well-understood infrastructures? Through an approach called Infrastructure-as-Code.

What is it that you need to do to do Infrastructure-as-Code? First and foremost, you must introduce tools which allow you to describe in a structured way what your infrastructure is supposed to look like, and let the tools do the heavy-lifting, as opposed to structuring your infrastructure design in your head and then build it manually.

Because this is the essence of the Infrastructure-as-Code principle:

Successful infrastructures are not the sum of all the things that a team *did* with the infrastructure, it is what a team *described or codified* as the target state of the infrastructure in such a clear and structured way that the actual doing can be delegated to specialized tools.

Let's illustrate this point. Imagine a simple web application infrastructure which consists of some web and some database servers, maybe some load balancers, and a certain network routing setup. Everything is set up correctly by hand, has been carefully tested, and is running smoothly.

Then, a new requirement appears: some servers which do not yet have a route on the network to another set of servers now need this route. No problem: someone from the sysops team adds the route definition by hand to the network setup, by hand. Everything is still working as desired.

But what if sometime later, the infrastructure encounters a problem, and has to be rebuilt, at least partly? Will the networking route which had been added be in place after the rebuild? Maybe, if the colleague who implemented it remembers to do so again. Maybe, if the route definition was part of a configuration backup that was made. Maybe, if the routing change has been documented.

From my personal experience with non-Infrastructure-as-Code setups I can report that typically, a lot of luck is involved in these cases. It's because the *desired target state* of such an infrastructure is only implicit - it is a floating state of knowledge distributed over the brains of the team members and glimmers of documentation here and there, never to be fully grasped, and re-achieved only by try-and-error whenever reloactions or outages demand a rebuild. And good luck onboarding new team members to such a setup.

On the opposite, Infrastructure-as-Code is the collection of an *explicitly codified target state* of the infrastructure. How this target state is achieved is up to the toolset used by the team.

In the course of this project, we will use *Terraform* to describe the target state of our AWS infrastructures.

A slightly simplified, yet typical Terraform declaration of the target state of a piece of infrastructure looks like this:

```hcl-terraform
resource "aws_instance" "bastion" {
  count = 1    
  instance_type = "t2.nano"    
  ami = "ami-12345"
    
  root_block_device {
    volume_type = "gp2"
    volume_size = "8"
    delete_on_termination = true
  }
}
```

This declares that the infrastructure must contain exactly one AWS EC2 instance named "bastion", which is of type *t2.nano*, running a certain AMI, with a *gp2* block device that is 8 GB in size and is deleted on instance termination.

Nothing here tells Terraform how to actually provision this instance. The *how* is Terraform's problem. We only declare the *what*, the desired target state. With this code checked into a version control system, this instance is now an explicit part of our infrastructure. It's not "the machine that Jim booted some month ago", long forgotten by everyone including Jim. If the instance is removed manually, or lost due to a crash, that's no big deal in terms of infrastructure management - Terraform will recreate it on its next run, because with the machine being lost, the desired target state is no longer fullfilled. And that's all what Terraform does: ensure the desired target state. It doesn't need a recipe for this ("if a machine is gone, follow this procedure") - all it needs is the codified design of the required infrastructure.

If we compare the two approaches of hand-crafted infrastructure versus Infrastructure-as-Code with each other, we can identify the following advantages of the latter:

- The desired target state is explicit
- Correctness does not depend on distributed knowledge
- Teams can focus on the *what* and leave the *how* to the tools
- New team members can browse the code and quickly get a full picture of the infrastructure
- It is easy to reconstruct which changes to the infrastructure were introduced when and by whom, because the infrastructure definition is a version-controlled code repository

Why is Infrastructure-as-Code an important pillar of a healthy Continuous Delivery setup? Because Continuous Delivery can only work if the environments through which deliveries run are well understood and predictable. At the end of the day, a Continuous Delivery setup is a bit like a production line in a factory - a lot of moving parts are involved, and they all need to be in exactly the right place for each and every delivery. The complexity such a setup, and the work involved in maintaining such a setup, which more often than not has to be done under time pressure, leaves no room for ambiguity and guesswork. Only an explicitly codified infrastructure design provisioned into life by dedicated tools can provides the required maintainability and stability over the lifespan of such a setup.

And, while not strictly a requirement to do Continuous Delivery successfully, it is a welcome side-effect that Infrastructure-as-Code setups tend to require much smaller operations teams than conventional setups: because teams need to put a lot less energy into the *how* of infrastructure provisioning (that's the job of the tools), and because team members need to juggle with a lot less implicit knowledge about the infrastructure design (that's all written down in the code base), a lot more productivit can be achieved even with a much smaller team.


## Immutable Servers

Infrastructure-as-Code setups love Immutable Servers. But first, what is an immutable server? Let's begin by looking at what a non-immutable server looks like. As an example, let's look at a typical web application server, for example one that hosts a PHP-based web app.

This server has a certain lifecycle. First and foremost, the server system itself is provisioned. Maybe as an EC2 instance via Terraform, if an Infrastructure-as-Code setup is already in place, maybe manually on-premise.

To fullfill its job, the server needs to be set up: an operating system needs to be installed and configured, the application has to be deployed onto the server, several services need to be launched. The server might be one of multiple systems which all look the same, running as a cluster behind a load-balancer, and therefore needs to be advertised to the load-balancer as a new origin server.

The interesting part in the lifecycle comes now: What happens with our server system when a new version of our application software is released, or if the system configuration itself (installed software packages, config files, etc.) needs to be updated?

In the classical approach, which is called *Phoenix Servers*, these changes happen in place, while the server system is running. A new application version is deployed, or other operation system software packages are updated, or config files changed and services restarted. This process can be made efficiently manageable by using configuration management software solutions like *Puppet*, *Chef*, *Ansible*, or *Salt*. The server itself lives on, probably for a very long time.

This works reasonably well and is an approach that is successfully applied for a lot of infrastructures worldwide.



-> "any change to a running system introduces risk" !!!


https://martinfowler.com/bliki/ImmutableServer.html