---
title: "Invoices"
description: "Here we describe the Seller API Invoice resource."
date: 2020-11-12T13:26:54+01:00
lastmod: 2020-11-12T13:26:54+01:00
draft: false
images: []
menu:
  docs:
    parent: "sellerapi"
    identifier: "invoices-sellerapi"
weight: 975
toc: true
---

Invoices track a collection of items bought from a store in a single
transaction. Invoices are also grouped further by a delivery type. For
instance, if someone buys a bike which is "Click and Collect" and a helmet
which is "Buy Online" in the same order and for the same retailer, these will
be split up into two invoices for the one retailer.

Invoices have many line items which represent the amount of a particular advert purchased in the invoice.

Invoices have many shipments which represent the individual shipments that were dispatched to a customer (including shipment tracking number and carrier).

Invoices can transition through the following statuses:

![Invoice Transitions](../images/transitions.png)

An invoice can be marked as "Ready" and then "Collected" only if the invoice is a "Click and Collect" invoice. Similarly, it can only be marked as "Sent" if it is a "Buy Online" invoice.

{{< alert icon="👉" text="<b>Take Note:</b> An invoice can also be marked as 'Partially Sent'. This state, however, can only be achieved by managing shipments via Shipments endpoint <a href='/v2/shipments/'>(/v2/shipments)</a> " />}}

An invoice will have both a `created_at` timestamp and a `paid_at` timestamp. You can only retrieve invoices after they have been paid, so you should use the `paid_at` timestamp as your canonical timestamp for the invoice.

## Listing invoices

This endpoint returns a paginated list of invoices.

### Request

    GET /api/v2/client/invoices

You may optionally choose to retrieve invoices which were **paid since** a certain time:

    GET /api/v2/client/invoices?since=2015-11-09T00:00:00Z

Or **paid before** a certain time:

    GET /api/v2/client/invoices?until=2015-11-10T00:00:00Z

Or **updated since** a certain time:

    GET /api/v2/client/invoices?updated_since=2015-11-10T00:00:00Z

Or **updated before** a certain time:

    GET /api/v2/client/invoices?updated_before=2015-11-10T00:00:00Z

Or which have a certain **status**:

    GET /api/v2/client/invoices?status=sent

Valid statuses are `paid`, `sent`, `partially_sent`, `ready`, `collected` and `refunded`.

You can also combine the terms as follows:

    GET /api/v2/client/invoices?updated_since=2015-11-10T00:00:00Z&status=sent


### Response

<div class="alert alert-success" role="alert">
  Status: 200 OK
</div>

```json
{
  "data": [
    {
      "id": 1,
      "type": "invoices",
      "attributes": {
        "postage": "10.0",
        "paid_at": "2021-05-23T13:07:07+10:00",
        "status": "paid",
        "delivery_type": "click_and_collect",
        "ready_at": null,
        "collected_at": null,
        "subtotal": "110.0",
        "discount": "10",
        "total": "100",
        "tax_total": "0.0",
        "created_at": "2021-05-23T13:07:07+10:00",
        "message_to_seller": "",
        "expected_delivery_date": "2021-05-27",
        "expected_delivery_slot": "Morning",
        "seller_id": 2
      }
    }
  ]
}
```

## Showing invoices

This endpoint will show an invoice's information and includes the invoices line items as well as shipments by default.

### Request

    GET /api/v2/client/invoices/1

### Response

<div class="alert alert-success" role="alert">
  Status: 200 OK
</div>

```json
{
  "links": {
    "self": "https://bikeexchange.com.au/api/v2/client/invoices/1"
  },
  "data": {
    "id": 1,
    "type": "invoices",
    "attributes": {
      "postage": "10.0",
      "paid_at": "2021-05-23T13:07:07+10:00",
      "status": "paid",
      "delivery_type": "click_and_collect",
      "ready_at": null,
      "collected_at": null,
      "subtotal": "110.0",
      "discount": "10",
      "total": "100",
      "tax_total": "0.0",
      "created_at": "2021-05-23T13:07:07+10:00",
      "message_to_seller": "",
      "expected_delivery_date": "2021-05-27",
      "expected_delivery_slot": "Morning",
      "seller_id": 2
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
      "shipments": {
        "data": [
          {
            "type": "shipments",
            "id": 1
          }
        ]
      },
      "customer": {
        "data": {
          "type": "customers",
          "id": "customer"
        }
      },
      "billing_customer": {
        "data": {
          "type": "customers",
          "id": "billing_customer"
        }
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
      "id": "customer",
      "type": "customers",
      "attributes": {
        "first_name": "Test",
        "surname": "User",
        "company_name": "Acme Pty Ltd",
        "email_address": "test@example.com",
        "phone": "0491570156",
        "address": "1 Example Road",
        "city": "Melbourne",
        "state": "Victoria",
        "postcode": "3000",
        "country": "Australia"
      }
    },
    {
      "id": "billing_customer",
      "type": "customers",
      "attributes": {
        "first_name": "Test",
        "surname": "User",
        "company_name": "Acme Pty Ltd",
        "email_address": "test+billing@example.com",
        "phone": "0491570156",
        "address": "1 Example Road",
        "city": "Melbourne",
        "state": "Victoria",
        "postcode": "3000",
        "country": "Australia"
      }
    },
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

## Sending an invoice

This endpoint marks "Buy Online" invoices as sent out to the customer. It can take additional attributes such as postage information.

### Request

    PUT /api/v2/client/invoices/1/sent

### Example

```json
{
  data: {
    type: "invoices",
    attributes: {
      postage_tracking: "TRACK007",
      postage_carrier: "AusPost",
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
    "self": "https://bikeexchange.com.au/api/v2/client/invoices/1"
  },
  "data": {
    "id": 1,
    "type": "invoices",
    "attributes": {
      "postage": "10.0",
      "paid_at": "2021-05-23T13:07:07+10:00",
      "status": "sent",
      "delivery_type": "click_and_collect",
      "ready_at": null,
      "collected_at": null,
      "subtotal": "110.0",
      "discount": "10",
      "total": "100",
      "tax_total": "0.0",
      "created_at": "2021-05-23T13:07:07+10:00",
      "message_to_seller": "",
      "expected_delivery_date": "2021-05-27",
      "expected_delivery_slot": "Morning",
      "seller_id": 2
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
      "shipments": {
        "data": [
          {
            "type": "shipments",
            "id": 1
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

## Readying an invoice

This endpoint marks "Click and Collect" invoices as ready for collection.

### Request

    PUT /api/v2/client/invoices/1/ready

### Response

<div class="alert alert-success" role="alert">
  Status: 200 OK
</div>

```json
{
  "links": {
    "self": "https://bikeexchange.com.au/api/v2/client/invoices/1"
  },
  "data": {
    "id": 1,
    "type": "invoices",
    "attributes": {
      "postage": "10.0",
      "paid_at": "2021-05-23T13:07:07+10:00",
      "status": "ready",
      "delivery_type": "click_and_collect",
      "ready_at": "2021-05-25T13:07:07+10:00",
      "collected_at": null,
      "subtotal": "110.0",
      "discount": "10",
      "total": "100",
      "tax_total": "0.0",
      "created_at": "2021-05-23T13:07:07+10:00",
      "message_to_seller": "",
      "expected_delivery_date": "2021-05-27",
      "expected_delivery_slot": "Morning",
      "seller_id": 2
    },
    "relationships": {
      "line_items": {
        "data": [
          {
            "type": "line_items",
            "id": 1
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
    }
  ]
}
```

## Marking an invoice as collected

This endpoint marks "Click and Collect" invoices as collected.

### Request

    PUT /api/v2/client/invoices/1/collected

### Response

<div class="alert alert-success" role="alert">
  Status: 200 OK
</div>

```json
{
  "links": {
    "self": "https://bikeexchange.com.au/api/v2/client/invoices/1"
  },
  "data": {
    "id": 1,
    "type": "invoices",
    "attributes": {
      "postage": "10.0",
      "paid_at": "2021-05-23T13:07:07+10:00",
      "status": "collected",
      "delivery_type": "click_and_collect",
      "ready_at": "2021-05-23T13:07:07+10:00",
      "collected_at": "2021-05-25T13:07:07+10:00",
      "subtotal": "110.0",
      "discount": "10",
      "total": "100",
      "tax_total": "0.0",
      "created_at": "2021-05-23T13:07:07+10:00",
      "message_to_seller": "",
      "expected_delivery_date": "2021-05-27",
      "expected_delivery_slot": "Morning",
      "seller_id": 2
    },
    "relationships": {
      "line_items": {
        "data": [
          {
            "type": "line_items",
            "id": 1
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
    }
  ]
}
```

## Deprecations

- 17/10/2019: The `postage_tracking` and `postage_carrier` fields in the body of invoice data can now return comma delimited values
if there was more than one shipment as part of an invoice.

- 10/07/2016: The 'tax_total' field in the body of line item data is deprecated,
  as we are now calculating tax at an invoice level only. This field will continue
  to be provided, but its value will always be zero. Please refer to the tax_total
  for the invoice, which is unchanged.