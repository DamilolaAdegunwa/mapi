---
title: "Adverts"
description: "Here we describe the Seller API Adverts resource."
date: 2020-11-12T13:26:54+01:00
lastmod: 2020-11-12T13:26:54+01:00
draft: false
images: []
menu:
  docs:
    parent: "sellerapi"
    identifier: "adverts-sellerapi"
weight: 930
toc: true
---

Adverts in Marketplacer represent an item, such as a bike or a chair. An
advert has one or more [variants](/v2/variants) which represent the different ways an advert
varies. For instance, a bike may come in Black or White colours, which would
mean the advert would have two variants: a White variant and a Black variant.

## List adverts

This endpoint will return a paginated collection of your adverts, as well as their associated brands and taxons.

### Request

    GET /api/v2/client/adverts

You can filter by `external_id` to return only adverts with that `external_id`.

    GET /api/v2/client/adverts?external_id=VALUE

You can also filter to only return adverts whose variants have a particular value for the `sku` or `barcode`.

    GET /api/v2/client/adverts?sku=VALUE

### Response

<div class="alert alert-success" role="alert">
  Status: 200 OK
</div>

```json
{
  "data": [
    {
      "id": 1,
      "type": "adverts",
      "attributes": {
        "title": "Giant Cypress DX",
        "description": "Smooth-rolling 700c wheels give Cypress...",
        "price": "499.0",
        "published": true,
        "sale_type": "classified",
        "sale_price": "350.0",
        "code": "ABC1234",
        "youtube_video_url": "https://www.youtube.com/watch?v=dQw4w9WgXcQ",
        "features": {
        }
      },
      "relationships": {
        "taxon": {
          "data": {
            "type": "taxons",
            "id": 1
          }
        },
        "brand": {
          "data": {
            "type": "brands",
            "id": 1
          }
        }
      }
    }
  ],
  "included": [
    {
      "id": 1,
      "type": "taxons",
      "attributes": {
        "name": "Hybrid Bikes",
        "tree_name": "Bikes - Hybrid Bikes",
        "slug": "hybrid-bikes"
      }
    },
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

## Get a single advert

This endpoint will return a single advert as well as its associated brands and taxons.

### Request

    GET /api/v2/client/adverts/1

You may also opt to include the variants, shipping dimensions, secondary taxons, or option values of this advert too:

    GET /api/v2/client/adverts/1?include=variants,shipping_parcel,secondary_taxons,option_values

### Response

{{< alert icon="👉" text="The response shown below contains variants, shipping dimensions, and secondary taxons, which are <em>not</em> included by default." />}}

<div class="alert alert-success" role="alert">
  Status: 200 OK
</div>

```json
{
  "links": {
    "self": "https://bikeexchange.com.au/api/v2/client/adverts/1"
  },
  "data": {
    "id": 1,
    "type": "adverts",
    "attributes": {
      "title": "Giant Cypress DX",
      "description": "Smooth-rolling 700c wheels give Cypress...",
      "price": "499.0",
      "published": true,
      "sale_type": "classified",
      "sale_price": "350.0",
      "code": "ABC1234",
      "youtube_video_url": "https://www.youtube.com/watch?v=dQw4w9WgXcQ",
      "features": {
      }
    },
    "relationships": {
      "variants": {
        "data": [
          {
            "type": "variants",
            "id": 1
          }
        ]
      },
      "taxon": {
        "data": {
          "type": "taxons",
          "id": 1
        }
      },
      "brand": {
        "data": {
          "type": "brands",
          "id": 1
        }
      },
      "shipping_parcel": {
        "data": {
          "type": "shipping_parcels",
          "id": 1
        }
      },
      "secondary_taxons": {
        "data": [
          {
            "type": "taxons",
            "id": 2
          }
        ]
      }
    }
  },
  "included": [
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
    },
    {
      "id": 1,
      "type": "taxons",
      "attributes": {
        "name": "Hybrid Bikes",
        "tree_name": "Bikes - Hybrid Bikes",
        "slug": "hybrid-bikes"
      }
    },
    {
      "id": 1,
      "type": "brands",
      "attributes": {
        "name": "Giant",
        "slug": "giant"
      }
    },
    {
      "id": 1,
      "type": "shipping_parcels",
      "attributes": {
        "weight": 5.0,
        "width": 8.3,
        "length": 98.3,
        "depth": 3.5,
        "mass_unit": "g",
        "distance_unit": "cm"
      }
    }
  ]
}
```

## Create an advert

Creating an advert is a multi-step process

Select the appropriate taxon & brand with the API only.

  1. Find the appropriate taxon and brand slugs by using the [Taxon API](/v2/taxons) and [Brand API](/v2/brands).
  2. Submit the advert to `/api/v2/client/adverts` as below, using the `taxon_slug` and `brand_slug` attributes.

Or, select the appropriate taxon & brand using the client admin in Marketplacer.

  1. Submit the advert to `/api/v2/client/adverts` as below, using the `map_taxon` and `map_brand` attributes.
  2. In the Marketplacer client admin `/client/mappings`, relate these values to the corresponding Marketplacer taxon and brand.

### Request

    POST /api/v2/client/adverts

### Input

Name | Type | Description
-----|------|--------------
`title`|`string` | **Required**. A short description of the advert.
`description`|`string` | **Required**. A longer description of the advert.
`price`|`float` | **Required**. Decimal separated with no signs or commas (i.e. 1234.56). The price of the advert, and the default price of all variants (unless `sale_price` is set).
`condition`|`string` | **Required**. `new` or `used`.
`taxon_slug`|`string` | **Required** if `published: true`. The slug of the taxon/category where this advert should appear under.
`brand_slug`|`string` | **Required** if `published: true`. The slug of the brand of this advert.
`specifications`|`string`| The specifications of the product.
`gender`|`string`| Indicates the gender that would use this product. Use one of 'female', 'male', or 'unisex'.
`year`|`string`| What year the product was created in.
`published`|`boolean`| Whether or not to display the advert immediately.
`sale_price`|`float` | Decimal separated with no signs or commas (i.e. 1234.56). The discounted price of the advert, applies to all variants unless they override the price.
`sale_type`|`string`|Allows you to specify whether you can buy online, click and collect etc. Permitted values are: `buy_online`, `buy_online_only`, `buy_online_or_click_and_collect ` (default if this value is not specified), `click_and_collect`, `classified`, `hire` and `in_store`
`domestic_shipping_cost`|`float`| Decimal separated with no signs or commas (i.e. 1234.56).  The additional domestic shipping cost for the advert, applies to all variants.
`international_shipping_cost`|`float`| Decimal separated with no signs or commas (i.e. 1234.56). The additional international shipping cost for the advert, applies to all variants.
`features`|`hash`| Includes two nested parameteters: `product_features`, which is an array of key features that this product has, and `product_details`, which is a hash composed of `option_type` keys and `option_value` values. See the example request below for how these nested params are structured.
`external_id`|`string`| Your system's identifier for this advert.
`map_taxon`|`string` or `array of strings`| The value/s to be mapped to the taxon. Prefer full paths.
`map_brand`|`string` or `array of strings`| The value/s to be mapped to the brand.
`code`|`string`| Optional - not shown to shoppers.
`notes`|`string`| Optional - not shown to shoppers.
`publish_streams`|`array of strings`| Optional - advert publication streams for `marketplace` and/or `m_store`, not shown to shoppers.
`feature_option_values`|`array of objects`| The id values of feature options for the product. The list of valid option values (with names) can be found using the [Taxon API](/v2/taxons). Please note that this is part of the `relationships` object rather than the `attributes` object.
`secondary_taxons`|`array of objects`| The slugs of taxons that you want to add as secondary taxons for the product. Please note that this is part of the `relationships` object rather than the `attributes` object.
`youtube_video_url`|`string`| Optional - the URL to a YouTube video highlighting this product.
`shipping_parcel_attributes`|`object`| Optional - object that contains shipping dimensions for the advert. These include `weight`, `width`, `length`, `depth`, `mass_unit`, and `distance_unit`. Valid values for `mass_unit` are 'g', 'kg', 'lb', and 'oz'. Valid values for `distance_unit` are 'mm', 'cm', 'm', 'in', 'ft', and 'yd'. This overrides the `shipping_parcel` attribute of the associated taxon if present.

### Example

```json
{
  data: {
    type: "adverts",
    attributes: {
      title: "Giant Cypress DX",
      description: "Smooth-rolling 700c wheels give Cypress...",
      price: 529,
      sale_price: 399,
      domestic_shipping_cost: 9.95,
      international_shipping_cost: 39.95,
      condition: "new",
      brand_slug: "giant",
      taxon_slug: "hybrid-bikes",
      published: true,
      external_id: "ABC1234",
      code: "",
      notes: "",
      shipping_parcel_attributes: {
        weight: 5.3,
        width: 8.3,
        length: 8.2,
        depth: 2.9,
        mass_unit: "g",
        distance_unit: "cm"
      },
      features: {
        product_features: ["7 gears", "Ready to use"],
        product_details: {
          shape: "Rectangle",
          texture: "Crisp as an autumn morning"
        }
      }
    },
    relationships: {
      feature_option_values: [
        {
          type: "option_values",
          "id": 214
        }
      ],
      secondary_taxons: [
        {
          type: "taxons",
          slug: "tricycles"
        }
      ]
    }
  }
}
```

or

```json
{
  data: {
    type: "adverts",
    attributes: {
      title: "Giant Cypress DX",
      description: "Smooth-rolling 700c wheels give Cypress...",
      price: 529,
      sale_price: 399,
      domestic_shipping_cost: 9.95,
      international_shipping_cost: 39.95,
      condition: "new",
      map_brand: ["Bikes - Hybrid Bikes", "Bikes - Giant Bikes"],
      map_taxon: ["Bikes - Hybrid Bikes", "Bikes - Giant Bikes"],
      published: false,
      publish_streams: ["m_store"],
      external_id: "ABC1234",
      code: "",
      notes: ""
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
    "self": "https://bikeexchange.com.au/api/v2/client/adverts/1"
  },
  "data": {
    "id": 1,
    "type": "adverts",
    "attributes": {
      "title": "Giant Cypress DX",
      "description": "Smooth-rolling 700c wheels give Cypress...",
      "price": "499.0",
      "published": true,
      "sale_type": "classified",
      "sale_price": "350.0",
      "code": "ABC1234",
      "youtube_video_url": "https://www.youtube.com/watch?v=dQw4w9WgXcQ",
      "features": {
      }
    },
    "relationships": {
      "taxon": {
        "data": {
          "type": "taxons",
          "id": 1
        }
      },
      "brand": {
        "data": {
          "type": "brands",
          "id": 1
        }
      }
    }
  },
  "included": [
    {
      "id": 1,
      "type": "taxons",
      "attributes": {
        "name": "Hybrid Bikes",
        "tree_name": "Bikes - Hybrid Bikes",
        "slug": "hybrid-bikes"
      }
    },
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

## Updating an advert

Use this endpoint to update any attributes for an advert.

To clear all `feature_option_values` or `secondary_taxons`, assign an empty array.
Not supplying the key for `feature_option_values` or `secondary_taxons` will leave the relevant relationship unchanged.

A request with the key below will remove all `feature_option_values` from the advert and leave `secondary_taxons` unchanged:

```json
{
  relationships: {
    feature_option_values: []
  }
}
```

A request with the key below will remove all `secondary_taxons` from the advert and leave `feature_option_values` unchanged:

```json
{
  relationships: {
    secondary_taxons: []
  }
}
```

A request with the field below won't change either relationship.

```json
{
  relationships: {}
}
```

### Request

    PUT /api/v2/client/adverts/1

### Example

```json
{
  data: {
    attributes: {
      title: "Giant Cypress DX 2014",
      features: { product_features: ["Feature 1", "Feature 2"] }
    },
    relationships: {
      feature_option_values: []
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
    "self": "https://bikeexchange.com.au/api/v2/client/adverts/1"
  },
  "data": {
    "id": 1,
    "type": "adverts",
    "attributes": {
      "title": "Giant Cypress DX 2014",
      "description": "Smooth-rolling 700c wheels give Cypress...",
      "price": "499.0",
      "published": true,
      "sale_type": "classified",
      "sale_price": "350.0",
      "code": "ABC1234",
      "youtube_video_url": "https://www.youtube.com/watch?v=dQw4w9WgXcQ",
      "features": {
        "product_features": [
          "Feature 1",
          "Feature 2"
        ]
      }
    },
    "relationships": {
      "taxon": {
        "data": {
          "type": "taxons",
          "id": 1
        }
      },
      "brand": {
        "data": {
          "type": "brands",
          "id": 1
        }
      }
    }
  },
  "included": [
    {
      "id": 1,
      "type": "taxons",
      "attributes": {
        "name": "Hybrid Bikes",
        "tree_name": "Bikes - Hybrid Bikes",
        "slug": "hybrid-bikes"
      }
    },
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

## Deleting an advert

Use this endpoint to delete an advert.

### Request

    DELETE /api/v2/client/adverts/1

### Response

<div class="alert alert-success" role="alert">
  Status: 204 No Content
</div>