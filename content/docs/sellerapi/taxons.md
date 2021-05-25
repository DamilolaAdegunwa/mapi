---
title: "Taxons"
description: "Here we describe the Seller API Taxon resource."
date: 2020-11-12T13:26:54+01:00
lastmod: 2020-11-12T13:26:54+01:00
draft: false
images: []
menu:
  docs:
    parent: "sellerapi"
    identifier: "taxons-sellerapi"
weight: 985
toc: true
---

Taxons in Marketplacer represent the categories for [adverts](/docs/sellerapi/adverts) in a heirarchical fashion. For instance, on Bike Exchange the "Hybrid Bikes" taxon is one of the children of the "Bikes" taxon.

## Listing taxons

This endpoint returns all taxons.

### Request

    GET /api/v2/taxons

### Response

<div class="alert alert-success" role="alert">
  Status: 200 OK
</div>

```json
{
  "data": [
    {
      "id": 1,
      "type": "taxons",
      "attributes": {
        "name": "Hybrid Bikes",
        "tree_name": "Bikes - Hybrid Bikes",
        "slug": "hybrid-bikes"
      }
    }
  ]
}
```

## Search for taxons

This endpoint allows you to search for a taxon by a word in its name or by a substring of its name.

### Request

If you want to search for the "Hybrid Bikes" taxon, make a query like this:

    GET /api/v2/taxons/search?name=Hybrid%20Bikes

If you want to search for all taxons that include the word "Hybrid":

    GET /api/v2/taxons/search?name=Hybrid

If you want to search by a substring of a word, add the `autocomplete` parameter, like:

    GET /api/v2/taxons/search?name=Bik&autocomplete=1

If you want to get the option value information for an individual taxon, use the taxon id or slug:

    GET /api/v2/taxons/4

or

    GET /api/v2/taxons/bikes

### Response

The response will include other taxons that may match your query.


<div class="alert alert-success" role="alert">
  Status: 200 OK
</div>

```json
{
  "data": [
    {
      "id": 1,
      "type": "taxons",
      "attributes": {
        "name": "Hybrid Bikes",
        "tree_name": "Bikes - Hybrid Bikes",
        "slug": "hybrid-bikes"
      }
    }
  ]
}
```