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

Here's why:

