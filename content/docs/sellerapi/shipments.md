---
title: "Shipments"
description: "Here we describe the Seller API Shipment resource."
date: 2020-11-12T13:26:54+01:00
lastmod: 2020-11-12T13:26:54+01:00
draft: false
images: []
menu:
  docs:
    parent: "sellerapi"
    identifier: "shipments-sellerapi"
weight: 977
toc: true
---

Shipments endpoint provides a way to create and retrieve information about individual shipments containing one or multiple line items
that are part of an invoice. Shipments have many shipped items (line items), which represent the amount of a particular advert purchased in the invoice.

An invoice will be marked as `Partially Sent` when some of its line items have been shipped. It will later transition into "Sent" when all of its line items have been shipped. Each shipment will have its "dispatched_at" canonical timestamp exposed, which represents the date and time when a shipment was created.

## Listing shipments

This endpoint returns a paginated list of shipments that belong to a particular invoice.

### Request

    GET /api/v2/client/invoices/:invoice_id/shipments

### Response

<div class="alert alert-success" role="alert">
  Status: 200 OK
</div>

```json
{
  "data": [
    {
      "id": 1,
      "type": "shipments",
      "attributes": {
        "shipment_tracking_number": "AusPost",
        "shipment_carrier": "12345",
        "dispatched_at": "2018-07-03T00:00:00.000+10:00"
      }
    }
  ]
}
```

## Showing shipments

This endpoint will show a shipment's information and includes the shipments line items as well as an invoice by default.

### Request

    GET /api/v2/client/invoices/:invoice_id/shipments/:shipment_id

### Response

<div class="alert alert-success" role="alert">
  Status: 200 OK
</div>

```json
{
  "links": {
    "self": "https://bikeexchange.com.au/api/v2/client/shipments/1"
  },
  "data": {
    "id": 1,
    "type": "shipments",
    "attributes": {
      "shipment_tracking_number": "AusPost",
      "shipment_carrier": "12345",
      "dispatched_at": "2018-07-03T00:00:00.000+10:00"
    },
    "relationships": {
      "line_items": {
        "data": [
          {
            "type": "line_items",
            "id": 1
          }
        ]
      },
      "invoice": {
        "data": {
          "type": "invoices",
          "id": 1
        }
      },
      "shipped_items": {
        "data": [
          {
            "type": "shipped_items"
          }
        ]
      }
    }
  },
  "included": [
    {
      "id": 1,
      "type": "line_items",
      "attributes": {
        "price": "100.0",
        "status": "allocated",
        "subtotal": "100.",
        "tax_total": "0.0",
        "original_tax_rate": null,
        "total": "100.0",
        "quantity": 1,
        "taxon_name": "Hybrid Bikes",
        "brand_name": "Giant",
        "advert_name": "Giant Cypress DX",
        "advert_external_id": "ABC123",
        "variant_name": "Navy Blue / Medium",
        "variant_sku": null,
        "variant_barcode": "08717611735314",
        "variant_external_id": "DEF123"
      }
    },
    {
      "id": null,
      "type": "shipped_items",
      "attributes": {
        "line_item_id": 2,
        "quantity": 1
      }
    }
  ]
}
```

## Create a shipment

This endpoint allows for the creation of shipments for a particular invoice. It requires a list of line item IDs (that are part of this invoice) to be provided. Additionally, it accepts shipment_tracking_number and shipment_carrier attributes. This will mutate an invoice's status and transition to either "Sent" or "Partially Sent".

{{< alert icon="👉" text="<b>Take Note:</b> 'line_item_ids' attribute is deprecated in favour of 'shipped_items' attribute as of February 2021. You'll need to provide 'line_item_id' and 'quantity' dispatched." />}}


### Request

    POST /api/v2/client/invoices/:invoice_id/shipments

### Example

```json
{
  data: {
    type: "shipments",
    attributes: {
      shipment_tracking_number: "TRACK007",
      shipment_carrier: "AusPost",
      line_items_ids: [1],
      shipped_items: [
        {
          line_item_id: 1,
          quantity: 1,
        }
      ]
    }
  }
}
```

### Response

<div class="alert alert-success" role="alert">
  Status: 201 Created
</div>

```json
{
  "links": {
    "self": "https://bikeexchange.com.au/api/v2/client/shipments/1"
  },
  "data": {
    "id": 1,
    "type": "shipments",
    "attributes": {
      "shipment_tracking_number": "AusPost",
      "shipment_carrier": "12345",
      "dispatched_at": "2018-07-03T00:00:00.000+10:00"
    },
    "relationships": {
      "line_items": {
        "data": [
          {
            "type": "line_items",
            "id": 1
          }
        ]
      },
      "invoice": {
        "data": {
          "type": "invoices",
          "id": 1
        }
      },
      "shipped_items": {
        "data": [
          {
            "type": "shipped_items"
          }
        ]
      }
    }
  },
  "included": [
    {
      "id": 1,
      "type": "line_items",
      "attributes": {
        "price": "100.0",
        "status": "allocated",
        "subtotal": "100.",
        "tax_total": "0.0",
        "original_tax_rate": null,
        "total": "100.0",
        "quantity": 1,
        "taxon_name": "Hybrid Bikes",
        "brand_name": "Giant",
        "advert_name": "Giant Cypress DX",
        "advert_external_id": "ABC123",
        "variant_name": "Navy Blue / Medium",
        "variant_sku": null,
        "variant_barcode": "08717611735314",
        "variant_external_id": "DEF123"
      }
    },
    {
      "id": null,
      "type": "shipped_items",
      "attributes": {
        "line_item_id": 2,
        "quantity": 1
      }
    }
  ]
}
```

## Update a shipment

This endpoint allows tracking information (number and carrier) to be updated for a particular shipment. Similarly to new shipments, it accepts shipment_tracking_number and shipment_carrier attributes. This will update tracking information and trigger email notification to a customer.

### Request

    PUT /api/v2/client/invoices/:invoice_id/shipments/:shipment_id

### Example

```json
{
  data: {
    type: "shipments",
    attributes: {
      shipment_tracking_number: "NEW_TRACK008",
      shipment_carrier: "AusPost"
    }
  }
}
```

### Response

<div class="alert alert-success" role="alert">
  Status: 200 OK
</div>

```json
{
  "links": {
    "self": "https://bikeexchange.com.au/api/v2/client/shipments/1"
  },
  "data": {
    "id": 1,
    "type": "shipments",
    "attributes": {
      "shipment_tracking_number": "AusPost",
      "shipment_carrier": "12345",
      "dispatched_at": "2018-07-03T00:00:00.000+10:00"
    },
    "relationships": {
      "line_items": {
        "data": [
          {
            "type": "line_items",
            "id": 1
          }
        ]
      },
      "invoice": {
        "data": {
          "type": "invoices",
          "id": 1
        }
      },
      "shipped_items": {
        "data": [
          {
            "type": "shipped_items"
          }
        ]
      }
    }
  },
  "included": [
    {
      "id": 1,
      "type": "line_items",
      "attributes": {
        "price": "100.0",
        "status": "allocated",
        "subtotal": "100.",
        "tax_total": "0.0",
        "original_tax_rate": null,
        "total": "100.0",
        "quantity": 1,
        "taxon_name": "Hybrid Bikes",
        "brand_name": "Giant",
        "advert_name": "Giant Cypress DX",
        "advert_external_id": "ABC123",
        "variant_name": "Navy Blue / Medium",
        "variant_sku": null,
        "variant_barcode": "08717611735314",
        "variant_external_id": "DEF123"
      }
    },
    {
      "id": null,
      "type": "shipped_items",
      "attributes": {
        "line_item_id": 2,
        "quantity": 1
      }
    }
  ]
}
```