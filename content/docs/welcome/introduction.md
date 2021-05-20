---
title: "Introduction"
description: "Welcome to the FakeCo API Documentation site. This is your 1 stop shop for all things related to the FakeCo APis."
date: 2020-10-06T08:48:57+00:00
lastmod: 2020-10-06T08:48:57+00:00
draft: false
images: []
menu:
  docs:
    parent: "welcome"
weight: 100
toc: true
---

Welcome to the FakeCo API Documentation site - here you'll find the resources necessary to start building with the FakeCo APIs.

### Our APIs

FakeCo has 2 APIs to choose from, the one you develop with will be driven by the use-cases that you are developing for - so choose wisely!

#### Marketplace API

This is our marketplace-wide API, examples of when you'd choose to use this API are:
- You are building a custom headless front end on top of FakeCo.
- You are integrating between FakeCo and an existing eCom Platform
- You need access to all data across the entire marketplace
- You want to automate marketplace categorization (taxonomy) creation

<div class="alert alert-warning" role="alert" >
  👉 Typically only the <b>Marketplace Operator</b> will use marketplace API.
</div>

**API Type:** GraphQL

[Get Started with the Marketplace API →]({{< relref "glossary" >}})

#### Seller API 

The Seller API is aimed (not surprisingly!) at individual Sellers on the Marketplace. Sellers can choose to use this API to help them integrate with other systems, or automate certain tasks. 

Examples of when you'd use the Seller API are:

- When you want to auto-create Products (Adverts)
- When you want to retrieve your Invoices
- When you want to set stock levels
- When you want to mark orders as shipped

**API Type:** REST

[Get Started with the Seller API →]({{< relref "glossary" >}})

#### Webhooks

Webhooks can be used at both a Marketplacer and Seller level. They provide a way to obtain near real-time event notifications on the marketplacer objects of your choosing. Additionally you can define both the event payloads as well as the events you want to be notified on. We currently support webhooks natively on the following objects:

- Adverts* (Products)
- Invoices
- Refund Requests
- Shipments*
- Variants

<div class="alert alert-warning" role="alert" >
  👉 Advert and Shipment objects not currently supported at the <b>Seller</b> level
</div>

[Get Started with Webhooks →]({{< relref "glossary" >}})

### At a Glance

As summary of the objects that each API can work with is shown below, remembering that that the _Seller API_ works only with an individual sellers data...

![Comparison of APIs](../glance.png)


