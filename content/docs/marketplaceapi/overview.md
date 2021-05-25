---
title: "Overview"
description: "Overview of the marketplace graphql api"
date: 2020-11-12T13:26:54+01:00
lastmod: 2020-11-12T13:26:54+01:00
draft: false
images: []
menu:
  docs:
    parent: "marketplaceapi"
    identifier: "overview-marketplaceapi"
weight: 610
toc: true
---

In this section you'll be provided an overview of the FakeCo Marektplace API.

## API Use Cases

This is our marketplace-wide API, examples of when you'd choose to use this API are:
- You are building a custom headless front end on top of FakeCo.
- You are integrating between FakeCo and an existing eCom Platform
- You need access to all data across the entire marketplace
- You want to automate marketplace categorization (taxonomy) creation

![Comparison of APIs](../images/marketplaceapi.png)

## GraphQL

This API complies with the [graphql](https://graphql.org/) specification. If you’ve not worked with GraphQL before, then we strongly recommend taking a look at these docs as they give you a good grounding on the general concepts you’ll need if you want to get the most out of working with the API.

We will of course provide you with the documentation you need to understand the specifics of how Marketplacer has implemented GraphQL!

Additionally, while you work with our GraphQL API, you may also find the following resources useful, both of which are auto generated from the Marketplacer GraphQL schema, and are therefore fully up to date:

- [Full Documentation](https://api.marketplacer.com/graphql/) Standard text-based docs
- [GraphQL Voyager](https://api.marketplacer.com/graphql-voyager/) An interactive visualization of our schema

## Tooling

When developing against the API you can use either:

- [Postman](https://www.postman.com/)
- [Insomnia](https://insomnia.rest/)

Both of which will read the GraphQL schema document, allowing for things like auto-completion and error detection. At the time of writing these features are better supported by Insomnia, although support in Postman appears to be improving.

The [example collection](https://api.marketplacer.com/postman/Marketplacer%20GraphQL%20API.postman_collection.json) of queries that we provide to help you get started, (see section below for more detail), are currently provided as a Postman collection.


## Content Type

API clients must supply a `Content-Type` header of `application/json` when [sending request data](https://graphql.org/learn/serving-over-http/#post-request):

![Content Type Header](../images/graphql_contenttype.png)

## Relay Specification

This API follows the [Relay Specification](https://relay.dev/graphql/connections.htm) for connections when returning data. This specification supports pagination using a cursor and allows traversal of data returned by the API.

## Authentication

To authenticate to the API, use an `Authorization` header:

![Bearer Auth](../images/graphql_bearertoken.png)

If your site is currently password protected, you will not be able to supply a username/password AND an API key. If this is the case, set the username/password using basic authentication and send the API key using the `MARKETPLACER-API-KEY` header:

![Basic Auth](../images/graphql_marketplacerkey.png)

Information on how to get your API key can be found in [Getting Started](/docs/marketplaceapi/gettingstarted)

## Postman Collection

[Postman](https://www.postman.com/downloads/) is a tool for developing and testing API integrations. It provides an interface for exploring APIs and executing requests against them. A postman collection is a document describing a series of API requests that can be imported into Postman to speed up this process. We have published a [Postman collection for the Marketplacer GraphQL API](https://api.marketplacer.com/postman/Marketplacer%20GraphQL%20API.postman_collection.json). It contains example query and mutation requests.

To import into Postman, download the `.json` file and go to the import menu in Postman:

![Postman Import](../images/graphql_importpostman.png)


Choose the `.json` file you downloaded and confirm that you wish to import it:

![Postman Confirmation](../images/graphql_dragpostman.png)


Edit the collection to fill in the variables required to access your site (see [the Postman documentation](https://learning.postman.com/docs/sending-requests/variables/#defining-collection-variables) for more information)).

Click the ellipsis on the imported collection and select edit

![Postman Variable Configuration](../images/graphql_editcollection.png)

Select the Variables tab, then edit the values in **Current Value**

![Postman Variable Configuration](../images/graphql_editvariables.png)

You will now see a series of requests in Postman which you can study and execute:

![Postman Request List](../images/graphql_apicollection.png)

---

### Collections Overview

While the queries and mutations in the collection are quite self-explanatory, we have provided some additional detail on what each does along with a few other hints and tips.

It is also worth remembering that the queries and mutations provided in the collection are just a _sample_ of what the GraphQL API can provide. For the full documentation, please refer to:

- [Full Documentation](https://api.marketplacer.com/graphql/) Standard text-based docs
- [GraphQL Voyager](https://api.marketplacer.com/graphql-voyager/) An interactive visualization of our schema

**Note:** Both of the above are based off the same GraphQL Schema, they just represent the information in different ways.

---

### Sellers

We have 2, (very similar!), example queries in the collection that allow you to return all, or some of the Sellers on the Marketplace.

- Get all Sellers
- Get all Sellers based on Business Name

It’s worth noting that you can search for Sellers based on more than just `businessName`, e.g. you can filter by:

- `updatedSince`
- `State`
- `Distance` (Geographic radius in which to look for Sellers, e.g. within 10KM)

_(These are just a sample of some of the filter criteria that can be used.)_

**Why would I use these?**

You would use these queries to allow you to obtain the `SellerId`, which you can then use as part of your other queries, e.g. `advertSearch` (by SellerId), to obtain all Adverts for a given Seller (or Sellers)

---

### Adverts

Adverts represent the Products or Services that a Seller may sell on a Marketplace. Going a little further an Advert, (e.g. for a Ski Jacket), may have multiple Variants, e.g.:

- Small / White
- Small / Black
- Large / White
- Large /Black

(You get the picture!)

The queries in this section all relate to how you can query the Adverts on the Marketplace, and further still, retrieve “child” objects of the Advert, e.g. the “Variants” of the Advert.  We have provided the following 3 example queries:

- Get all Adverts by Keyword
- Get all Adverts for a Seller (using the “SellerId")
- Get all Adverts “updated since”

Looking more closely at the collection, you’ll notice we actually make use of 2 different “queries”:

- `advertSearch`
- `allAdverts` 

We have summarised their differences below, and why you’d use 1 over another

| Query | What it does | Why You’d Use |
|-------|--------------|---------------|
| `advertSearch` | Returns only published adverts | Use this if you want to perform detailed filtering across a number of attributes, .e.g.: `Brand`, `Country`, `maxPrice` etc. Again, this query only returns Adverts that are _published_ on the marketplace, i.e. those adverts that are viewable and represent items for sale. |
| `allAdverts` | Returns Published, Unpublished and Deleted Adverts | You would use this query when you need access to every advert on the platform irrespective of whether it’s published or not. You can only query by the time the advert was last updated.|

---

### Cart

A Cart represents a collection of items, (in this case _Variants_), that a consumer wishes to purchase. The collection has 3 _mutations_ and 1 query related to the Cart:

- Add a Variant, (a “purchasable item”), to a new Cart
- Add a Variant to an existing Cart
- Checkout the Cart (and create and Order)
- Search for a Cart, (based on the CartID)

> Up until this point we have only discussed _reading data_, (via queries). Anytime we need to add, change or delete data via GraphQL, we use _Mutations_. For more information on the differences between queries and mutations, refer to the [official GraphQL Documentation](https://graphql.org/).

Creating a new Cart, will result in a `CartID` being returned as part of the response payload. You can then use this id to work with the other mutations and queries.

**Note:** Again, this is just a sample selection of what you can do with a Cart, for example you can update items in an existing Cart and well as remove items from a Cart.

---

### Order

The final items in our collection relate to an Order, an Order represents the purchase of one or more Variants, (aka Line Items), across 1 or more Sellers. We have 1 mutation and 1 query in our collection:

- Create a new Order
- Retrieve an Order

> It’s worth noting that you can create an Order using either the `orderCreate` mutation in this section, or as previously mentioned by using the `cartCheckout` mutation. It will depend on your own particular use-case which approach you want to use.

Taking a closer look at the `orderCreate` mutation, you’ll note that you can add multiple Variants to the Order in the `LineItems` section:

    
    lineItems: [
        {             
            variantId: "VmFyaWFudC0yODE="
            quantity: 1
            totalCents: 22500
            postageCents: 1000 
        },
        {             
            variantId: "VmFyaWFcbC0yEDE="
            quantity: 2
            totalCents: 50000
            postageCents: 1000
        }
    ]
    

If these 2 Variants relate to 2 different Adverts, from 2 different Sellers, then 2 Separate _Invoices_ will be created for the Order. (An Invoice is part of an Order that is handled by a particular Seller).

This relationship is shown below:

![Order Relationship](../images/order-relationship.png)

It’s also worth noting that separate invoices will be created for the _same seller_ if _different delivery methods_ have been chosen for Line Items, e.g.:

-	Line Item 1 – Delivery Method = “Deliver”
-	Line Item 2 – Delivery Method = “Click and Collect”  

This will result in 2 separate Invoices even if the Line Items have been sold by the 1 Seller.

**Creating Invoices Manually**

In the example above we have shown how you can add Line Items (Variants) directly on the Order, (and that Invoices will be auto-generated), you can however take an alternative approach and create an Order with Invoices and then add Line items to the Invoice. So, we in effect we have 2 approaches:

- Create Line Items directly on the Order as per the example, and Invoices will be created for you.
- Create Invoices “manually” on the Order and add Line Items to the Invoices

**Note:** these approaches are mutually exclusive, you can use one or the other, but not both in the same mutation.

**Order Query**

Using the Order query, you can return the _child objects_ of the Order, including:

- Invoices
- Line Items
- Shipments

## Getting Support

If you encounter any issues with using this API, please contact our suppport team at [support@marketplacer.com](mailto:support@marketplacer.com) with:

1. An example of what request you're making
2. What headers + body are being returned from the server
3. What you expect to happen
