---
date: 2018-01-17T16:13:00+01:00
lastmod: 2018-01-17T16:13:00+01:00
title: "Healthy Continuous Delivery: Immutable Server"
authors: ["manuelkiessling"]
slug: the-five-pillars-of-a-healthy-continuous-delivery-setup-part-1-infrastructure-as-code
draft: true
---

## Preface

This is part two in a five parts series.


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




## Immutable Servers

Infrastructure-as-Code setups love Immutable Servers. But first, what is an immutable server? Let's begin by looking at what a non-immutable server looks like. As an example, let's look at a typical web application server, for example one that hosts a PHP-based web app.

This server has a certain lifecycle. First and foremost, the server system itself is provisioned. Maybe as an EC2 instance via Terraform, if an Infrastructure-as-Code setup is already in place, maybe manually on-premise.

To fullfill its job, the server needs to be set up: an operating system needs to be installed and configured, the application has to be deployed onto the server, several services need to be launched. The server might be one of multiple systems which all look the same, running as a cluster behind a load-balancer, and therefore needs to be advertised to the load-balancer as a new origin server.

The interesting part in the lifecycle comes now: What happens with our server system when a new version of our application software is released, or if the system configuration itself (installed software packages, config files, etc.) needs to be updated?

In the classical approach, which is called *Phoenix Servers*, these changes happen in place, while the server system is running. A new application version is deployed, or other operation system software packages are updated, or config files changed and services restarted. This process can be made efficiently manageable by using configuration management software solutions like *Puppet*, *Chef*, *Ansible*, or *Salt*. The server itself lives on, probably for a very long time.

This works reasonably well and is an approach that is successfully applied for a lot of infrastructures worldwide.



-> "any change to a running system introduces risk" !!!


https://martinfowler.com/bliki/ImmutableServer.html