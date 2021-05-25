---
title: "Brands"
description: "Here we describe the Seller API Brand resource."
date: 2020-11-12T13:26:54+01:00
lastmod: 2020-11-12T13:26:54+01:00
draft: false
images: []
menu:
  docs:
    parent: "sellerapi"
    identifier: "brands-sellerapi"
weight: 940
toc: true
---

Brands in Marketplacer represent the brands for [adverts](/docs/sellerapi/adverts).

## Listing brands

This endpoint returns all brands.

### Request

    GET /api/v2/brands

### Response

<div class="alert alert-success" role="alert">
  Status: 200 OK
</div>

```json
{
  "data": [
    {
      "id": 1,
      "type": "brands",
      "attributes": {
        "name": "Giant",
        "slug": "giant"
      }
    }
  ]
}
```

## Search for brands

This endpoint allows you to search for a brand by its name or by a substring of its name.

### Request

If you wish to search for the "Giant" brand, make a query like this:

    GET /api/v2/brands/search?name=Giant

If you wish to search by a substring, add the `autocomplete` parameter, like this:

    GET /api/v2/brands/search?name=Gia&autocomplete=1

### Response


<div class="alert alert-success" role="alert">
  Status: 200 OK
</div>

```json
{
  "data": [
    {
      "id": 1,
      "type": "brands",
      "attributes": {
        "name": "Giant",
        "slug": "giant"
      }
    }
  ]
}
```