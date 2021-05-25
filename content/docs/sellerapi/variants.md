---
title: "Variants"
description: "Here we describe the Seller API Variant resource."
date: 2020-11-12T13:26:54+01:00
lastmod: 2020-11-12T13:26:54+01:00
draft: false
images: []
menu:
  docs:
    parent: "sellerapi"
    identifier: "variants-sellerapi"
weight: 990
toc: true
---

Variants represent the ways an [advert](/docs/sellerapi/adverts) can vary. For instance, an advert might
come in different colours and sizes. Each colour and size combination is a
unique variant. These are represented by option types and option values.
Option types are things such as "Colour" and "Size", and the option values are
the corresponding values for these types. For example, an advert might come in
"Red" and sizes "Small", "Medium" and "Large". This would be represented by 3
variants:

* Colour: Red, Size: Small
* Colour: Red, Size: Medium
* Colour: Red, Size: Large

Variants can represent advert variations by more than simply colour and size, too. For instance,
groupsets on Bike Exchange can vary based on thread, crank arm length,
cassette ratio, front rotor size, rear rotor size, crankset ratio and front
deraillieur.

Adverts that do not have anything to vary by still need to have a single variant - this will hold barcodes, stock, etc.

---

## Variant IDs and External IDs

Some endpoints use a variant `id` (e.g. updating a variant). The `id` of a variant will *not* always be consistent after some UI operations or exporting then reimporting as CSV. Please assign an `external_id` to variants you want to keep track of as they relate to the data in your system. To find the current `id` value of a variant whose `external_id` you know, you can use the filtered list endpoint: `GET /api/v2/client/variants?external_id=VALUE`.

If you ask for a variant ID that that doesn't exist for this authorized user, or for that specific advert if an advert ID was given, then the request will return a 404.

## Deprecations

- 29/03/2016: The `upc` and `ean` fields have been deprecated in favour of the
  `barcode` field. This is a GTIN, which can contain either UPC-12, EAN-8,
  EAN-13, or ITF-14 barcodes. We will still support these fields in API
  requests, but values will be stored under the `barcode` field.
- 18/06/2019: The `weight`, `width`, `length`, and `depth` fields have been deprecated in favour of the `shipping_parcel_attributes` fields of the same name. We will still accept these fields in API requests, but the values will not be saved.

## List your variants

This endpoint will return a paginated collection of your variants.

### Request

    GET /api/v2/client/variants

You can filter by `external_id`, `sku`, and `barcode` to return only variants with that value.

    GET /api/v2/client/variants?external_id=VALUE

### Response

<div class="alert alert-success" role="alert">
  Status: 200 OK
</div>

```json
{
  "data": [
    {
      "id": 1,
      "type": "variants",
      "attributes": {
        "count_on_hand": 10,
        "sku": "SKU002",
        "barcode": "9300601 123457",
        "price": 30.0,
        "sale_price": 15.0,
        "ean": "4003994155486",
        "upc": "9300601123456"
      }
    }
  ]
}
```

## List an advert's variants

This endpoint will return a list of a specific advert's variants.

### Request

    GET /api/v2/client/adverts/1/variants

### Response

<div class="alert alert-success" role="alert">
  Status: 200 OK
</div>

```json
{
  "data": [
    {
      "id": 1,
      "type": "variants",
      "attributes": {
        "count_on_hand": 10,
        "sku": "SKU002",
        "barcode": "9300601 123457",
        "price": 30.0,
        "sale_price": 15.0,
        "ean": "4003994155486",
        "upc": "9300601123456"
      }
    }
  ]
}
```

## Get a single variant

This endpoint shows data for a specific variant

See Variant IDs and External IDs for why an ID you thought was there might return 404.

### Request

    GET /api/v2/client/variants/1

You can alternatively scope the variant lookup to an advert:

    GET /api/v2/client/adverts/1/variants/2

You may also opt to include the shipping dimensions of this variant:

    GET /api/v2/client/variants/1?include=shipping_parcel

### Response

```json
{
  "links": {
    "self": "https://bikeexchange.com.au/api/v2/client/variants/1"
  },
  "data": [
    {
      "id": 1,
      "type": "variants",
      "attributes": {
        "count_on_hand": 10,
        "sku": "SKU002",
        "barcode": "9300601 123457",
        "price": 30.0,
        "sale_price": 15.0,
        "ean": "4003994155486",
        "upc": "9300601123456"
      },
      "relationships": {
        "option_values": {
          "data": [
            {
              "type": "option_values",
              "id": 1
            }
          ]
        },
        "shipping_parcel": {
          "data": {
            "type": "shipping_parcels",
            "id": 1
          }
        }
      }
    }
  ],
  "included": [
    {
      "id": 1,
      "type": "option_values",
      "attributes": {
        "name": "Small",
        "presentation": "Small"
      },
      "relationships": {
        "option_type": {
          "data": {
            "type": "option_types",
            "id": 1
          }
        }
      }
    },
    {
      "id": 1,
      "type": "option_types",
      "attributes": {
        "name": "Size",
        "presentation": "Size",
        "feature": false,
        "variant": true
      },
      "relationships": {
        "option_values": {
          "data": [
            {
              "type": "option_values",
              "id": 1
            }
          ]
        }
      }
    },
    {
      "type": "shipping_parcels",
      "id": 1,
      "weight": 5.0,
      "width": 8.3,
      "length": 98.3,
      "depth": 3.5,
      "mass_unit": "g",
      "distance_unit": "cm"
    }
  ]
}
```

## Creating a variant

Creating a variant is a two-step process. First you must make a request to get a list of the variant's possible option types and option values:

### Request #1

    GET /api/v2/client/adverts/1/variants/new

### Response #1

<div class="alert alert-success" role="alert">
  Status: 200 OK
</div>

```json
{
  "data": {
    "id": 1,
    "type": "option_types",
    "attributes": {
      "name": "Size",
      "presentation": "Size",
      "feature": false,
      "variant": true
    },
    "relationships": {
      "option_values": {
        "data": [
          {
            "type": "option_values",
            "id": 1
          }
        ]
      }
    }
  },
  "included": [
    {
      "id": 1,
      "type": "option_values",
      "attributes": {
        "name": "Small",
        "presentation": "Small"
      },
      "relationships": {
        "option_type": {
          "data": {
            "type": "option_types",
            "id": 1
          }
        }
      }
    }
  ]
}
```

Once you have this information, you can create a new variant:

### Request #2

    POST /api/v2/client/adverts/1/variants

### Input

Name | Type | Description
-----|------|--------------
`count_on_hand`|`string` | **Required**. How much of this variant is in stock currently.
`infinite`|`boolean`| Optional - Indicates whether the product has infinite stock, or can be sold with 0 stock (default: false)
`barcode`|`string` | The barcode for this particular variant.
`sku`|`string` | The SKU / manufacturer's part number for this variant.
`external_id`|`string`| Your system's identifier for this variant.
`notes`|`string`| Optional - not shown to shoppers
`price`|`float`| Decimal separated with no signs or commas (i.e. 1234.00). The price of this variant, which overrides the advert's price.
`sale_price`|`float`| Decimal separated with no signs or commas (i.e. 1234.00). The sale price of this variant.
`ean`|`string`| DEPRECATED: Please use `barcode` instead.
`upc`|`string`| DEPRECATED: Please use `barcode` instead.
`option_values`|`array of objects`| The id values of option_values for the product. The list of valid option values (with names) can be found using the `/api/v2/client/adverts/1/variants/new` path (see above). Please note that this is part of the `relationships` object rather than the `attributes` object.

### Example

```json
{
  data: {
    type: "variants",
    attributes: {
      count_on_hand: 5,
      barcode: "9300601 123456",
      sku: "SKU001",
      external_id: "ABC1234",
      notes: ""
    },
    relationships: {
      option_values: [
        { type: "option_values", id: 1 }
      ]
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
    "self": "https://bikeexchange.com.au/api/v2/client/variants/1"
  },
  "data": [
    {
      "id": 1,
      "type": "variants",
      "attributes": {
        "count_on_hand": 10,
        "sku": "SKU002",
        "barcode": "9300601 123457",
        "price": 30.0,
        "sale_price": 15.0,
        "ean": "4003994155486",
        "upc": "9300601123456"
      },
      "relationships": {
        "option_values": {
          "data": [
            {
              "type": "option_values",
              "id": 1
            }
          ]
        }
      }
    }
  ],
  "included": [
    {
      "id": 1,
      "type": "option_values",
      "attributes": {
        "name": "Small",
        "presentation": "Small"
      },
      "relationships": {
        "option_type": {
          "data": {
            "type": "option_types",
            "id": 1
          }
        }
      }
    },
    {
      "id": 1,
      "type": "option_types",
      "attributes": {
        "name": "Size",
        "presentation": "Size",
        "feature": false,
        "variant": true
      },
      "relationships": {
        "option_values": {
          "data": [
            {
              "type": "option_values",
              "id": 1
            }
          ]
        }
      }
    }
  ]
}
```

## Updating a variant

Use this endpoint to update a variant's information, such as its `count_on_hand`.

See Variant IDs and External IDs for why an ID you thought was there might return 404.

### Request

    PUT /api/v2/client/adverts/1/variants/1

    PUT /api/v2/client/variants/1

### Example


  You only need to pass through option values to this request and you can leave out option types. This is because option values belong to an option type; if we know the option value, we know the option type.


```json
{
  data: {
    type: "variants",
    attributes: {
      count_on_hand: 0,
      infinite: true,
      barcode: "9300601 123456",
      sku: "SKU001"
    },
    relationships: {
      option_values: [
        { type: "option_values", id: "1" }
      ]
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
    "self": "https://bikeexchange.com.au/api/v2/client/variants/1"
  },
  "data": [
    {
      "id": 1,
      "type": "variants",
      "attributes": {
        "count_on_hand": 10,
        "sku": "SKU002",
        "barcode": "9300601 123457",
        "price": 30.0,
        "sale_price": 15.0,
        "ean": "4003994155486",
        "upc": "9300601123456"
      },
      "relationships": {
        "option_values": {
          "data": [
            {
              "type": "option_values",
              "id": 1
            }
          ]
        }
      }
    }
  ],
  "included": [
    {
      "id": 1,
      "type": "option_values",
      "attributes": {
        "name": "Small",
        "presentation": "Small"
      },
      "relationships": {
        "option_type": {
          "data": {
            "type": "option_types",
            "id": 1
          }
        }
      }
    },
    {
      "id": 1,
      "type": "option_types",
      "attributes": {
        "name": "Size",
        "presentation": "Size",
        "feature": false,
        "variant": true
      },
      "relationships": {
        "option_values": {
          "data": [
            {
              "type": "option_values",
              "id": 1
            }
          ]
        }
      }
    }
  ]
}
```

## Updating stock on hand for many variants

Use this endpoint to update the `count_on_hand` for many variants.

### Request

    POST /api/v2/client/variants/stock_on_hand

### Example

  You should pass a hash of `variant_id` to `count_on_hand`. There is a special case of `INFINITE` which will set the `infinite?` stocklevel flag.


```json
{
  count_on_hand: {
    "123": 2,
    "456": 10,
    "789": "INFINITE"
  }
}
```

### Response

<div class="alert alert-success" role="alert">
  Status: 201 Created
</div>


### Example with incorrect data

```json
{
  count_on_hand: {
    "123": "OTHER",
    "456": 10,
    "789": "INF"
  }
}
```

### Response

<div class="alert alert-danger" role="alert">
  Status: 422 Unprocessable Entity
</div>

## Deleting a variant

Use this endpoint to delete a variant.

See **_Variant IDs and External IDs_** for why an ID you thought was there might return 404.

### Request

    DELETE /api/v2/client/adverts/1/variants/2

    DELETE /api/v2/client/variants/2

### Repsonse

<div class="alert alert-success" role="alert">
  Status: 200 No Content
</div>