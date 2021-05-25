---
title: "Overview"
description: "Overview of the marketplace graphql api"
date: 2020-11-12T13:26:54+01:00
lastmod: 2020-11-12T13:26:54+01:00
draft: false
images: []
menu:
  docs:
    parent: "sellerapi"
    identifier: "overview-sellerapi"
weight: 910
toc: true
---

This section describes the resources that make up the FakeCo Seller API (sometimes refered to as the V2 API).

## API Use Cases

The Seller API is aimed (not surprisingly!) at individual Sellers on the Marketplace. Sellers can choose to use this API to help them integrate with other systems, or automate certain tasks. 

Examples of when you'd use the Seller API are:

- When you want to auto-create Products (Adverts)
- When you want to retrieve your Invoices
- When you want to set stock levels
- When you want to mark orders as shipped

![Comparison of APIs](../images/sellerapi.png)

## JSON API

This API complies with the [jsonapi.org](http://jsonapi.org) specification.
Please consult its documentation to learn about it if you have not done so
already.

## Evolvability and Versioning

This API will evolve over time and new fields will be added to responses as new features are implemented. Ensure that any software parsing API responses is able to handle new fields being added at any time.

If it is necessary to remove or change the meaning or data-type of a field, we will inform customers of a change and continue supporting the old behaviour until all clients have had a chance to update.

If you wish to receive these notifications, please let us know at [support@marketplacer.com](mailto:support@marketplacer.com)

## Rate Limits

There is a rate limit of 50 transactions per second on all our REST endpoints.

## Content Type

API clients must supply a `Content-Type` header of `application/vnd.api+json` when [sending request data](http://jsonapi.org/format/#content-negotiation-clients):

{{< btn-copy text="curl -H 'Content-Type: application/vnd.api+json' -d '{ ... }' -X POST https://bestfriendbazaar.com/api/v2/client/adverts" >}}

```bash
$ curl -H "Content-Type: application/vnd.api+json" -d "{ ... }" -X POST https://bestfriendbazaar.com/api/v2/client/adverts
```

## Pagination

Some resources may be paginated. Each paginated collection is limited to 100 resources at a time. A paginated resource will always contain a `links` key on the top-level which contains `first`, `next` and `last` keys:

```json
{
  links: {
    "first": "[route]?page[number]=1&page[size]=100",
    "next": "[route]?page[number]=2&page[size]=100",
    "last": "[route]?page[number]=2&page[size]=100"
  }
}
```

The "first" link returned here is the link to the first page of this reosurce. The "next" link is the next page of this resource, and the "last" link indicates the last page of results that will show results.

For instance, to paginate through the [Variants](/v2/variants) endpoint, you can make requests as so:

```bash
  GET https://bestfriendbazaar.com/api/v2/client/variants?page[number]=1
  GET https://bestfriendbazaar.com/api/v2/client/variants?page[number]=2
  ...
  GET https://bestfriendbazaar.com/api/v2/client/variants?page[number]=n
```

You can also change the number of items returned in one request by using the `page[size]` parameter. This example will only show 10 items at a time:

```bash
  GET https://bestfriendbazaar.com/api/v2/client/variants?page[size]=10
```

## Authentication

To authenticate to the API, use an `Authorization` header:

{{< btn-copy text="curl -H 'Authorization: Bearer api_key' https://bestfriendbazaar.com/api/v2/client/adverts" >}}

```bash
$ curl -H "Authorization: Bearer api_key" https://bestfriendbazaar.com/api/v2/client/adverts
```

If your site is protected by basic authentication, that will conflict with the `Bearer` method. In that case, send your API key using `MARKETPLACER-API-KEY`:

{{< btn-copy text="curl -u username:password -H 'MARKETPLACER-API-KEY: api_key' https://bestfriendbazaar.com/api/v2/client/adverts" >}}

```bash
$ curl -u username:password -H "MARKETPLACER-API-KEY: api_key" https://bestfriendbazaar.com/api/v2/client/adverts
```

Information on how to create your `api_key` can be found in [Getting Started](/v2/getting_started)

## Postman Collection

[Postman](https://www.postman.com/downloads/) is a tool for developing and testing API integrations. It provides an interface for exploring APIs and executing requests against them. A postman collection is a document describing a series of API requests that can be imported into Postman to speed up this process. We have published a [Postman collection for the Marketplacer v2 API](https://api.marketplacer.com/postman/Marketplacer%20API%20v2.postman_collection.json) It contains example requests for a number of the API endpoints.

To import into Postman, download the `.json` file and go to the import menu in Postman:

![Postman Import](../../marketplaceapi/images/graphql_importpostman.png)


Choose the `.json` file you downloaded and confirm that you wish to import it:

![Postman Confirmation](../images/sellerapi_dragdroppostman.png)


Edit the collection to fill in the variables required to access your site (see [the Postman documentation](https://learning.postman.com/docs/sending-requests/variables/#defining-collection-variables) for more information)).

Click the ellipsis on the imported collection and select edit

![Postman Variable Configuration](../images/sellerapi_editcollection.png)

Select the Variables tab, then edit the values in **Current Value**

![Postman Variable Configuration](../../marketplaceapi/images/graphql_editvariables.png)

You will now see a series of requests in Postman which you can study and execute:

![Postman Request List](../images/sellerapi_apicollection.png)

## Getting Support

If you encounter any issues with using this API, please contact our suppport team at [support@marketplacer.com](mailto:support@marketplacer.com) with:

1. An example of what request you're making
2. What headers + body are being returned from the server
3. What you expect to happen