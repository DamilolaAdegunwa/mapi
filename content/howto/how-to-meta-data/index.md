---
title: "How to add meta data"
description: "In this article you’ll learn about adding metadata to your orders, invoices and sellers using the marketplace api"
date: 2020-11-12T13:26:54+01:00
lastmod: 2020-11-12T13:26:54+01:00
draft: false
images: []
menu:
  main:
    parent: "howto"
    identifier: "metadata-howto"
weight: 215
toc: true
---

### What you’ll learn

In this article you’ll learn about adding metadata to your orders, invoices and sellers, specifically:

- Using `orderCreate` to create an order with metadata
- Using `updatedOrders` or `order` to retrieve that metadata
- Using `metadataUpsert` to add metadata to an existing `order`, `invoice` or `seller`


### What is metadata and why would you use it

The `metadata` object allows you to add key-value pairs to the following object types:

- `order`
- `invoice`
- `seller`

Its intended purpose is to allow you to define and use attributes that may not exist for that object in the default schema.

In this article we show you how to add metadata at the point of order creation using the `orderCreate` mutation. Additionally we show you how to add metadata to _existing_ orders, invoices and sellers using the `metadataUpsert` mutation.

**Note:** the `orderCreate` mutation does allow you to manually create `invoice` objects, and therefore you can add metadata to the `invoice` at this point too, however we do not cover that use-case in this article.


### Using orderCreate

An example `orderCreate` mutation is shown below with the `metadata` object being used to add 2 (example), keys:

- `createdBy`
- `mode`

```graphql
    mutation{
      orderCreate(input: {
        order: {   
          firstName: "John"
          surname: "Doe"
          phone: "0405123456"
          emailAddress: "john@email.com"
          billingFirstName: "John"
          billingSurname: "Doe"
          messageToSeller: "This is a test order on Wed 19th Feb"
          billingEmailAddress: "john@email.com"
          address: {
            address: "1 Bourke Street"
              city: "Melbourne"
              country: {
                code: "AU"
              }
              postcode: "3000"
              state: {
                name: "victoria"
              }
          }
          metadata: [{
            key: "createdBy"
            value: "ERP"
          },
          {
            key: "mode"
            value: "Real_Time"
          }
          ]
          termsAndConditionsAccepted: true
          }
        lineItems: [{             
          variantId: "VmFyaWFudC00ODE3"
          quantity: 1
          totalCents: 22500
          postageCents: 1000 
        }]
      })
      {
        order{
          id
        }
      }
    }
```

<br/> 

As you can see, you are able to create any number of key / value pairs as required.

### Retrieving metadata

Returning this data via a GraphQL query is no more complex than any other, an example using the `updatedOrders` query is illustrated below, but the same syntax would be used for other queries referencing the `order` object:

```graphql
    query{
      updatedOrders(updatedSince: "2021-02-19"){
        nodes {
          id
          metadata{
            key
            value
          }
        }
      }
    }
```

### Upserting on existing objects

In addition to adding metadata at the point of object creation, you can "upsert" metadata on existing objects. (Upserting is when you add/insert data when it _doesn't_ already exist, and update when it _does_).

The mutation shown below is straightforward, all you have to provide by way of inputs are:

- The `owner` (ID) of the object (`order`, `invoice` or `seller`), that you want to work with
- The `key` for the metadata
- The `value` for the `key`


```graphql
    mutation{
      metadataUpsert(input: 
        {owner: "U2VsbGVyLTc4", key: "CreditRating", value: "Rubbish"})
      {
        status
        metadata{
          key
          value
        }
      }
    }
```

### Considerations

The use of metadata as described in this article is intended to be used only within the context of the GraphQL API, i.e.:

- The metadata is not accessible via the Markeptlacer Web frontend
- The metadata is not available via the v2 (REST) API