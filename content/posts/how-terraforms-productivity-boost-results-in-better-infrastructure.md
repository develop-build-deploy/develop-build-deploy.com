---
date: 2019-01-08T08:34:00+01:00
lastmod: 2019-01-08T08:34:00+01:00
title: "How Terraform's productivity boost results in better infrastructure"
description: ''
authors: ["manuelkiessling"]
slug: how-terraforms-productivity-boost-results-in-better-infrastructure
draft: true
---

Recently, [Klint Finley](https://twitter.com/klintron) from WIRED did a short interview with me for [an article he was writing about Hashicorp](https://wired.com/) - he had found me through [a comment I left on Hacker News](https://news.ycombinator.com/item?id=18361660), where I wrote that *"if there is one software company out there that deserves all the love and support (and money) the tech world can give, then it's HashiCorp"*.

I would like to reiterate a point which I couldn't quite make during the interview, but which became more clear to me afterwards.

At one point I told Klint how Terraform, HashiCorp's Infrastructure-as-Code solution, doesn't really bring anything new to the table in terms of possibilities - you can define and manage your cloud infrastructure with Terraform, but you can just as well define and manage your infrastructure yourself, using, say, `aws-cli` and shell scripting.

Thus, Terraform doesn't have any kind of *functionality* that magically makes your infrastructure better. Nothing stops you from creating awesome infrastructures without Terraform. However, in practice, using Terraform results in a kind of emergent quality which more often than not **will** make you end up with a better infrastructure.

The reason is that the enormous productivity boost you gain by choosing Terraform over some home-grown scripting solution makes it very easy to Do The Right Thing™.

Let me give you an example.

A common pattern when building even small cloud infrastructures is to set up a *bastion host* structure, where you create a virtual machine which has a very basic setup and only listens for incoming SSH connections, providing and running no other network services, and make this the only machine that is publicly reachable. The machines that run your applications and services - which have a much larger attack surface precisely because they are running a lot more software that is way more complex - are only available on the internal network. The public bastion host, which additionally has a network interface on the internal network, is then used to allow SSH access to the internal machines without exposing them on the public internet.

When you create such a setup, it is good practice to set up firewall rules between the bastion host and the internal machines which ensure that SSH connections to the internal machines must originate from the internal network address of the bastion host, and are disallowed from any other components
