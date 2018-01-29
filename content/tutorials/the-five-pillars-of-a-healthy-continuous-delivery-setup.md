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

The goal of this project is to teach you how to build successful Continuous Delivery setups for your software products. To do so, we need to build with a shared understanding of the different practices and principles that together are the foundation for Continuous Delivery setups that are stable, maintainable, and adaptable.

I call these *the Five Pillars of a healthy Continuous Delivery setup*.

The pillars are:

* Infrastructure-as-Code
* Zero-Downtime Blue-Green Deployment
* Immutable Servers
* Software Tests
* Software-based database Migrations

This is not an ordered list. All five practices and principles are equally important for a successful setup, and they reinforce each other to form a working whole.

Let's have a closer look at each:


## Infrastructure-as-Code

Running a Continuous Delivery setup successfully always means running systems infrastructures (server systems, network rules, DNS entries, firewall settings, logging setups etc.) that is completely under your control, with no surprises lurking in dark corners.

Why? Because at the end of the day, systems infrastructures are the playing field on which the Continuous Delivery game is played. And it is only played well if the playing field is kept in excellent condition.

You will use parts of these infrastructures to execute the Continuous Delivery setup itself, and you will use other parts as the delivery goal of your setup, the place where your applications and services will run.

If you don't have full control over these, you are out of the game: you will not be able to reliably deliver new functionality again and again with confidence if servers don't look the way you expect, if firewall rules are not in place as needed, or if DNS entries are a mess. 

