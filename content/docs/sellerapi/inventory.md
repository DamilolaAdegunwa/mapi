---
title: "Inventory"
description: "Here we describe the Seller API Inventory resource."
date: 2020-11-12T13:26:54+01:00
lastmod: 2020-11-12T13:26:54+01:00
draft: false
images: []
menu:
  docs:
    parent: "sellerapi"
    identifier: "inventory-sellerapi"
weight: 970
toc: true
---

Inventory represents the stock holding that a specific seller has for a multi-store
advert [variant](/v2/variants). For instance, a parent seller might have an advert with
two variants: "Blue / Small" and "Red / Large"; Seller A might hold 2 of the "Blue / Small"
variant while Seller B holds 3 of the "Red / Large" variant. This would be represented by 
2 inventory items:

* Seller A, "Blue / Small", 2 on hand
* Seller B, "Red / Large", 3 on hand

You can retrieve the seller IDs related to the multi-store using [multi_store_memberships](/v2/multi_store_memberships).

An inventory item can have a different price/sale price from the parent variant. It can also 
be configured to have infinite stock which never sells out.

{{< alert icon="👉" text="<b>IMPORTANT:</b> This endpoint only deals with <b>multi-store</b> adverts. For single seller adverts, stock levels are set directly on the <b>variant</b>." />}}

## List a variant's inventory items

### Request

    GET /api/v2/client/variants/1/inventories

### Response

<div class="alert alert-success" role="alert">
  Status: 200 OK
</div>

```json
{
  "data": [
    {
      "id": 1,
      "type": "inventories",
      "attributes": {
        "count_on_hand": 10,
        "price": "500",
        "sale_price": "450",
        "infinite": false,
        "seller_id": 1
      }
    }
  ]
}
```

## Updating a variant's inventory

This route can be used by:

* The multi-store parent to update or create multiple inventories
* Members of the multi-store to update or create a single inventory record associated with their multi-store membership.

### Request
    PUT /api/v2/client/variants/1/inventories

### Multi Store Owner / Parent Example

{{< alert icon="👉" text="<b>TAKE NOTE:</b> If you do not send a record for a seller who already has stock in the system, that seller's stock will remain unchanged. To set a seller as having no stock, you must explicitly send a 0 value." />}}


```json
{
  data: [{
    type: "inventories",
    attributes: {
      count_on_hand: 10,
      price: 500,
      sale_price: 450,
      seller_id: 1
    }},
    {
    type: "inventories",
    attributes: {
      count_on_hand: 15,
      seller_id: 2
    }},
    {
    type: "inventories",
    attributes: {
      infinite: true,
      seller_id: 3
    }}
  ]
}
```

### Multi Store Owner / Parent Response

<div class="alert alert-success" role="alert">
  Status: 200 OK
</div>

```json
{
  "data": [
    {
      "id": 1,
      "type": "inventories",
      "attributes": {
        "count_on_hand": 10,
        "price": "500",
        "sale_price": "450",
        "infinite": false,
        "seller_id": 1
      }
    },
    {
      "id": 2,
      "type": "inventories",
      "attributes": {
        "count_on_hand": 15,
        "price": null,
        "sale_price": null,
        "infinite": false,
        "seller_id": 2
      }
    },
    {
      "id": 3,
      "type": "inventories",
      "attributes": {
        "infinite": true,
        "seller_id": 3
      }
    }
  ]
}
```

### Multi Store Member / Child Example

{{< alert icon="👉" text="<b>TAKE NOTE:</b> This request will either update or create an Inventory associated with the member's API key in the request." />}} 

The owner of the API key will need to already be a member of the multi-store. The attributes must be submitted in an array, and the last element will be used to update the multi-store member's inventory. All inventories will be returned.

```json
{
  data: [
    {
      attributes: {
        count_on_hand: 22,
        price: 27.38,
        sale_price: 21.99,
        infinite: false
      }
    }
  ]
}
```

### Multi Store Member / Child Response
  
<div class="alert alert-success" role="alert">
  Status: 200 OK
</div>

```json
{
  "data": [
    {
      "id": 1,
      "type": "inventories",
      "attributes": {
        "count_on_hand": 22,
        "price": "27.38",
        "sale_price": "21.99",
        "infinite": false,
        "seller_id": 1
      }
    },
    {
      "id": 2,
      "type": "inventories",
      "attributes": {
        "count_on_hand": 15,
        "price": null,
        "sale_price": null,
        "infinite": false,
        "seller_id": 2
      }
    },
    {
      "id": 3,
      "type": "inventories",
      "attributes": {
        "infinite": true,
        "seller_id": 3
      }
    }
  ]
}
```

## Deleting an inventory record

  An inventory record with a 0 count_on_hand value is handled differently to a non-existent inventory record. The 0 value will show the seller in the store selector but disabled; a non-existent record means the seller won't be shown at all in the store selector.


### Request

    DELETE /api/v2/client/variants/1/inventories/1

### Reponse
<div class="alert alert-success" role="alert">
  Status: 204 No Content
</div>