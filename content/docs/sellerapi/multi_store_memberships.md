---
title: "Multi Store Memberships"
description: "Here we describe the Seller API Multi Store Membership resource."
date: 2020-11-12T13:26:54+01:00
lastmod: 2020-11-12T13:26:54+01:00
draft: false
images: []
menu:
  docs:
    parent: "sellerapi"
    identifier: "multistore-sellerapi"
weight: 980
toc: true
---

A seller can be the parent of one multi store. A multi store membership represents the
relationship between a parent store and a child store.

The parent can use the [inventory](/docs/sellerapi/inventory) endpoint to manage the stock levels
for the child stores. To do that, the parent must know the Seller ID of the child store. 

This endpoint allows the parent to retrieve the IDs and names of the child stores.

## List the members of the multi store

### Request

    GET /api/v2/client/multi_store_memberships

### Response

<div class="alert alert-success" role="alert">
  Status: 200 OK
</div>

```json
{
  "data": [
    {
      "id": 1,
      "type": "multi_store_memberships",
      "attributes": {
        "seller_id": 1,
        "seller_name": "Bob's Bikes"
      }
    }
  ]
}
```