---
title: "Getting Started"
description: "Get going with the seller REST api"
date: 2020-11-12T13:26:54+01:00
lastmod: 2020-11-12T13:26:54+01:00
draft: false
images: []
menu:
  docs:
    parent: "sellerapi"
    identifier: "gettingstarted-sellerapi"
weight: 920
toc: true
---

The FakeCo Seller API allows developers to connect in to a seller account on a Marketplace site.

This tutorial will cover the steps required to create an advert and a variant using the Seller API.

**Please note** that the Seller API currently supports **product** adverts only, (as opposed to **sevice** type adverts).

The first thing that is required to connect to the API is an API key.

## Generating an API key

You can get one of these by logging into the seller portal on a FakeCo site, clicking the three dot menu in the top right and selecting "API & Integrations":

![API Documentation link](../images/sellerapi_keygen.png)

After you click this link, you will see the "API Access & Documentation" page.

![API Documentation](../images/sellerapi_gennewkey.png)

Clicking "Generate API Key" on this page will generate your API key and display it to you, along with the endpoint that you must use to access the API.

![API key generated](../images/sellerapi_keyinfo.png)

We'll make our first request to the API with the API key and endpoint now.

## Testing our API key

Before we create new adverts, we will make sure that our API key works. To do that, we'll make a call to the API to retrieve adverts for our account. To make that request, we'll use the endpoint and API key that we just saw, along with the `curl` command.

    curl -H "Authorization: Bearer c62b790..." \
      https://bestfriendbazaar.com/api/v2/client/adverts

In the first part of this command, we're passing an `Authorization` header through. This is done so that the API knows who we are.

**Please note** that, if your site is currently password protected, you will not be able to supply a username/password AND an API key. If this is the case, set the username/password using basic authentication (using the `-u` option in curl) and send the API key using the `MARKETPLACER-API-KEY` header (`-H MARKETPLACER-API-KEY: c62b790...` in curl).

At the end of the command, we specify the URL that we would like to request. When we make this request, we'll see this response:

    {"data":[]}

Or, if your account has adverts already, you will see those adverts returned along with some `links` metadata. This metadata is explained further in [the API documentation](/v2/adverts/)

Now that we have confirmed that our API key is working, let's create an advert.

## Creating an advert

In this section, we'll add a new advert. The advert will be for a Giant Cypress DX bike, which is a hybrid bike.

Before we can issue a request to the API to create an advert, we will first need to lookup for the relevant taxon and brand slugs. A taxon in Marketplacer can be thought of as a category for the advert. A brand represents the manufacturer/brand of the product. We cannot create an advert without linking it to both a brand and a taxon.

**Note:** We don't need to do a request for both taxons and brands if we know their slugs already. For instance, if we want to create another Giant hybrid bike, then we could just make the request to create the advert without making requests for the brands and taxons again.

The taxon that we'll be looking for is the "Hybrid Bikes" taxon. We can search for it by issuing a request like this:

    curl -H "Authorization: Bearer c62b790..." \
      https://bestfriendbazaar.com/api/v2/taxons/search?name=Hybrid%20bik

**Note:** The `name` parameter here is URI-encoded. We've used a partial name here to demonstrate that the taxon search endpoint matches on the beginning of the name. You could also search less specifically by adding the `autocomplete` parameter. e.g.

    curl -H "Authorization: Bearer c62b790..." \
      https://bestfriendbazaar.com/api/v2/taxons/search?name=ybri&autocomplete=1

This request will return a list of taxons that best match the search terms:

```json
{
  data: [
    {
      type: "taxons",
      id: "4",
      attributes: {
        name: "Hybrid Bikes",
        tree_name: "Bikes - Hybrid Bikes",
        slug: "hybrid-bikes"
      }
    }
  ]
}
```

You must select from these the taxon that best matches your product.

We must now do a similar request to get our brand; this time to `/api/v2/brands/search`.

    curl -H "Authorization: Bearer c62b790..." \
      https://bestfriendbazaar.com/api/v2/brands/search?name=Giant

The response we'll look very similar to the taxon response:

```json
{
  data: [
    {
      type: "brands",
      id: "42",
      attributes: {
        name: "Giant",
        slug: "giant"
      }
    }
  ]
}
```

You can use the `autocomplete` parameter on brand search just as you can with taxon search.

Now that we know the correct brand and taxon slugs, we can create our advert. The bike that we're selling is a Giant Cypress DX. We'll sell it for $499 and its a new bike. With all that in mind, we can build a request to create a bike. This request will be a `POST` request.

    curl -i \
      -H "Authorization: Bearer c62b790..." \
      -H "Content-Type: application/vnd.api+json" \
      https://bestfriendbazaar.com/api/v2/client/adverts -d '
    {
      "data": {
        "type": "adverts",
        "attributes": {
          "title": "Giant Cypress DX",
          "description": "Smooth-rolling 700c wheels give Cypress...",
          "price": 499,
          "condition": "new",
          "taxon_slug": "hybrid-bikes",
          "brand_slug": "giant"
        }
      }
    }'

One thing to note about this request is that we're passing through a `Content-Type` header as well as our typical `Authorization` header. This `Content-Type` header needs to be passed through so that the Marketplacer server knows that we're submitting a JSON blob. `POST` and `PUT` requests that do not use this header will cause Marketplacer sites to return a `415 Unsupported Media Type` header, along with an error:

```json
{
  errors: [
    {
      title: "Unsupported media type",
      detail: "All requests must use the 'application/vnd.api+json' Content-Type. ...",
      id: nil,
      href: nil,
      code: 415,
      source: nil,
      links: nil,
      status: "unsupported_media_type"
    }
  ]
}
```

Because we're specifying the correct `Content-Type` header, this request will suceeed:

    HTTP/1.1 200 OK

Underneath, we'll see a JSON representation of the advert that was just created:

```json
{
  links: {
    self: "https://bestfriendbazaar.com/api/v2/client/adverts/102743768-giant-giant-cypress-dx"
  },
  data: {
    type: "adverts",
    id: "102743768",
    attributes: {
      title: "Giant Cypress DX",
      description: "Smooth-rolling 700c wheels give Cypress...",
      sale_type: "classified",
      published: false,
      code: ""
    }
  }
}
```

The returned information will also include taxon and brand relationships. You can see this demonstrated in the full example output, [shown under the "Response" header for "Create an advert"](/v2/adverts/#create-an-advert).

Note that the `published` field here has a value of `false`. This means that the advert is not yet visible on the site. We can toggle this advert to be published by making this request:

    curl -i -X PUT -H "Authorization: Bearer c62b790..." \
      -H "Content-Type: application/vnd.api+json"
      https://bestfriendbazaar.com/api/v2/client/adverts/102743768 \
      -d '{ "data": { "attributes": { published": true } } }'

A couple of things to note about this request:

1. We're using `-X PUT`. This changes the method of the request to be `PUT` rather than `POST`. Using a `POST` here will return a 404 from the server instead.
2. We're using the advert's ID here: `/api/v2/client/adverts/102743768`. We first saw this in the response from when we created an advert.

When we make this request, we'll see that it fails:

    HTTP/1.1 422 Unprocessable Entity
    ...

    {
      "errors": [
        {"title": "Validation error",
        "detail":" Missing variants Size must be selected",
        "id": null,
        "href": null,
        "code": 422,
        "source":"data/attributes/missing_variants",
        "status":"resource_invalid"}
      ]
    }

This request is failing because the advert is missing variants. Variants in Marketplacer are used to differentiate between the different variations of an advert. In this case, bikes in the "Hybrid Bikes" taxon, must have at least one variant which indicates what size the bike is available in. Due to the fact that this advert does not have any variants at all, we cannot toggle the published field at this moment.

For more information about the adverts API, please read the [Adverts API documentation](/v2/adverts).

To fix this, we're going to need to create a variant.

## Creating a variant

To create a variant in Marketplacer, we first need an advert. We created this in the last step. The process to create a variant requires a couple of steps.

1. Fetching the list of option types and option values.
2. Creating the variant

Option types and option values within Marketplacer represent the ways that a variant can vary. For instance, an option type could be "Size", where its related option values would be "Small", "Medium" and "Large". To get the list of option types and option values for a new variant, we can make this request:

    curl -H "Authorization: Bearer c62b790..." \
    https://bestfriendbazaar.com/api/v2/client/adverts/102743768/variants/new

This will return JSON like this:

    {
      "data": [
        {
          "type": "option_types",
          "id": "2",
          "attributes": {
            "name": "Bike Size",
            "presentation": "Size"
          },
          "relationships": {
            "option_values": {
              "data": [
                {
                  "type": "option_values",
                  "id": "3",
                  "attributes": {
                    "name": "XXS",
                    "presentation": "XXS"
                  }
                },
                {
                  "type": "option_values",
                  "id": "4",
                  "attributes": {
                    "name": "XS",
                    "presentation": "XS"
                  }
                },
    ...

You must select an option value to use from this list. Let's use the "XS" size here, which has an ID of 4, to create a new variant for our Giant Cypress DX advert. The request we'll make to do that is this:

    curl -i -H "Authorization: Bearer c62b790..." \
      -H "Content-Type: application/vnd.api+json" \
      https://bestfriendbazaar.com/api/v2/client/adverts/102743768/variants \
      -d '{
      "data": {
        "type": "variants",
        "attributes": {
          "count_on_hand": 1,
          "barcode": "9300601 123456"
        },
        "relationships": {
          "option_values": [
            { "type": "option_values", "id": 4 }
          ]
        }
      }
    }
    '

After this request succeeds, we'll see our new variant:

```json
{
  links: {
    self: "https://bestfriendbazaar.com/api/v2/client/adverts/102743768-giant-giant-cypress-dx/variants/2446836",
  },
  data: {
    type: "variants",
    id: "2446836",
    attributes: {
      sku: "",
      barcode: "9300601 123456",
      count_on_hand: 1,
    },
    relationships: {
      option_values: {
        data: {
          type: "option_values",
          id: 6
        }
      }
    }
  }
}
```

For more information about the variants API, please read the [Variants API documentation](/v2/variants).

Our advert now has a variant associated with it. This means that we will now be able to publish this advert. Let's make that request to publish the advert again:

    curl -X PUT -i -H "Authorization: Bearer c62b790..." \
      -H "Content-Type: application/vnd.api+json" \
      https://bestfriendbazaar.com/api/v2/client/adverts/102743768 \
      -d '{ "data": { "attributes": { published": true } } }'

This time, our request will succeed:

    HTTP/1.1 200 OK
    ...

This means that our advert is now published. We can view this advert by logging into the seller portal and then clicking "My Adverts" in the sidebar.

![My Adverts](../images/sellerapi_myadverts.png)

If we then click "Preview" next to the ad:

![Preview an advert](../images/sellerapi_invalid.png)

We'll see that our advert now shows the information that we supplied when we created it:

![Our bike](../images/sellerapi_advertdetail.png)

You'll notice that it's missing a cruicial piece: an image! Let's upload one now.

## Adding an image to an advert

Let's assume that we already have an image for this bike handy in our current directory and the image is called `bike.jpg`. We can attach it to our advert by running this `curl` command:

    curl -i -H 'Authorization: Bearer c62b790...' \
      -F data[attributes][image]=@bike.jpg \
      https://bestfriendbazaar.com/api/v2/client/adverts/102743768/images

The response we'll get back looks like this:

    HTTP/1.1 200 OK

    ...

    {
      "data": {
        "type": "images",
        "id": "3868b196624595aa78823e667cf59c03",
        "attributes": {
          "url": "/dbimages/bike/fn_2/768/102743768/bike.jpg"
        }
      }
    }

This JSON blob tells us where we can find the image that we've just uploaded relative to `bestfriendbazaar.com`.

We can upload up to 20 images to adverts on Marketplacer, but for now we'll be happy with just one. You can also upload PDF [documents](/v2/documents).

## Conclusion

In this tutorial we've seen how we can create an advert with a variant and an image. If you want to find out more about the API, read the [rest of the documentation](/v2).