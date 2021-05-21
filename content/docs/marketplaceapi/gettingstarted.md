---
title: "Getting Started"
description: "Get going with the martketplace graphql api"
date: 2020-11-12T13:26:54+01:00
lastmod: 2020-11-12T13:26:54+01:00
draft: false
images: []
menu:
  docs:
    parent: "marketplaceapi"
    identifier: "getstarted-marketplaceapi"
weight: 620
toc: true
---

The Marketplacer GraphQL API allows developers to connect to a Marketplacer site to retrieve and modify data. Using this API it is possible to build a custom front-end to Marketplacer, to connect to a different eCommerce system, syndicate products, power a native mobile app and more.

This tutorial will cover the steps required to start querying data using the Marketplacer GraphQL API.

The first thing that is required to connect to the API is an API key.

## Getting your API key

Your site has API keys that are accessible under the Configuration menu in the administration portal.

![API link](../images/graphql_keygen.png)

We recommend providing a description for your API key so you know what purpose it was created for. 

**Please note** that when you generate an API key, the key will only be shown once. If you forget the key, you will need to generate a new key.

We'll make our first query to the API with the API key now.

## Testing our API key

We can test that our API key works by doing a search for adverts in the site. The `/graphql` endpoint on your site is used for all GraphQL queries and mutations. We can use the Insomnia client to query for adverts.

![Advert Search query](../images/graphql_testendpoint.png)

To authenticate to the API, use an `Authorization` header:

![Bearer Auth](../images/graphql_bearertoken.png)

If your site is currently password protected, (the staging environment for example) you will not be able to supply a username/password AND an API key. If this is the case, set the username/password using basic authentication and send the API key using the `MARKETPLACER-API-KEY` header:

![Basic Auth](../images/graphql_keywithbasicauth.png)

Here we did a very simple query which asks just for the advert's title and the name of the taxon to which it is associated. A `taxon` is the category which is associated with an advert.

    {
      advertSearch(attributes: {keywordQuery: "Boots"}) {
        adverts {
          edges {
            node {
              title
              taxon {
                displayName
              }
            }
          }
        }
      }
    }

We can add additional attributes to the query and retrieve more data:

    {
      advertSearch(attributes: {keywordQuery: "Boots"}) {
        adverts {
          edges {
            node {
              title
              lowestPriceCents
              taxon {
                displayName
                urlSlug
              }
            }
          }
        }
      }
    }

## Understanding Edges and Nodes

The Marketplacer GraphQL API follows the [Relay Specification](https://relay.dev/graphql/connections.htm). This defines how data is returned when a one-to-many relationship is involved and is useful for paginating results. In this design, a node has attributes as well as connections to other nodes. A node represents one objects while an edge is the relationship between two nodes.

![Edges and Nodes](../images/edges-nodes.png)

In the diagram above, `Advert`, `Variant` and `Image` are all nodes and the links between the nodes are all edges. When navigating through the data structure returned by the Marketplacer GraphQL API you will need to use the node and edge concepts to get to the data you're interested in.

## Queries and Mutations

In the previous examples we performed a Query against the GraphQL API. As its name suggests, a Query is a way to retrieve data from the API. Which attributes are returned is determined by the query you perform.

If you wish to update data, you need to use a Mutation instead. An example of a Mutation in the Marketplacer GraphQL API is `cartAddCartItem` which adds an item to a cart you have created for a user.

## IDs

The Node interface defined by Relay gives all objects a globally unique ID that can be used for looking up objects. This ID must be unique across all entities in the API - an advert cannot have the same ID as a Variant or an Order, for example. When given an ID, the API must be able to fetch the right object without knowing the type of object. This means that the IDs used in the GraphQL API are not the same IDs used in the admin & seller portals. 

{{< alert icon="💡" text="Where an object returned by the API also has an ID that might be used by an admin or seller, we usually return that ID in the <b>legacyId</b> attribute." />}}

## Example Mutation

In this section we will create a cart and add an item to that cart. This will be a two-step process. First we must find a variant which can be added to our cart. We will then create a cart and add the variant to it.

### Finding a variant

A variant represents a purchasable variation of a product. For example, it may represent the small size of a t-shirt. Even if there are no size variations, an advert will always have a variant which is the item that must be added to the cart.

We can perform a search for adverts and request variants to be returned:

    {
      advertSearch(attributes: {keywordQuery: "Dog"}) {
        adverts {
          edges {
            node {
              title
              variants {
                edges {
                  node {
                    id
                  }
                }
              }
            }
          }
        }
      }
    }

We get returned an advert including a variant.

    {
      "data": {
        "advertSearch": {
          "adverts": {
            "edges": [
              {
                "node": {
                  "title": "Organic Dog Treats",
                  "variants": {
                    "edges": [
                      {
                        "node": {
                          "id": "VmFyaWFudC01NA=="
                        }
                      }
                    ]
                  }
                }
              }
            ]
          }
        }
      }
    }

We now have the ID of a variant that we can add to cart. 

### Creating a cart with an item

The cartCreate mutation lets us create a cart and add a variant to it:

    mutation {
      cartCreate(input: {cartItemsToAdd: [{variantId: "VmFyaWFudC01NA==", quantity: 1}]}) {
        status
        cart {
          id
        }
      }
    }

As well as the status, we retrieve the ID of the cart (remember that IDs are globally unique) so we can refer to this cart again when we add more items or start the checkout process.

## Further reading

The Marketplacer GraphQL API covers the queries and mutations you need to search, browse and display adverts and sellers as well as creating carts and orders. Read the [full documentation](https://api.marketplacer.com/graphql/) to find out more, or interactively explore the schema with [GraphQL Voyager](https://api.marketplacer.com/graphql-voyager/).