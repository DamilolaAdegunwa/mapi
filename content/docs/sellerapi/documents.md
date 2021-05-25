---
title: "Documents"
description: "Here we describe the Seller API Document resource."
date: 2020-11-12T13:26:54+01:00
lastmod: 2020-11-12T13:26:54+01:00
draft: false
images: []
menu:
  docs:
    parent: "sellerapi"
    identifier: "documents-sellerapi"
weight: 960
toc: true
---

Documents are displayed on an [advert](/docs/sellerapi/adverts)'s page. An advert can have up to 20 documents.

## Listing an advert's documents

### Request

    GET /api/v2/client/adverts/1/documents

### Response

<div class="alert alert-success" role="alert">
  Status: 200 OK
</div>

```json
{
  "data": [
    {
      "id": "1",
      "type": "documents",
      "attributes": {
        "filename": "size-guide.pdf",
        "path": "Ff/FFF-3Ff333fFffFFf3-F3FFfF.pdf",
        "url": "https://marketplacer.imgix.net/Ff/FFF-3Ff333fFffFFf3-F3FFfF.pdf?s=11111111111111"
      }
    }
  ]
}
```

## Showing a single document

This endpoint allows you to request a document at a specific position within the adverts list of documents. The example request here shows how to get the first document for an advert. Numbers up to 20 are supported.

### Request

    GET /api/v2/client/adverts/1/documents/1

### Response

<div class="alert alert-success" role="alert">
  Status: 200 OK
</div>

```json
{
  data: {
    id: "1",
    type: "documents",
    attributes: {
      filename: "size-guide.pdf",
      path: "Ff/FFF-3Ff333fFffFFf3-F3FFfF.pdf",
      url: "https://marketplacer.imgix.net/Ff/FFF-3Ff333fFffFFf3-F3FFfF.pdf?s=11111111111111"
    }
  }
}
```

## Adding a document

This endpoint allows you to add up to 20 documents to an advert. For document uploads you may need to set the Content-Type to be 'multipart/form-data'.

### Request

    POST /api/v2/client/adverts/1/documents

### Input

Name | Type | Description
-----|------|--------------
`document`|`file`| The document data to add to the advert.
`document_url`|`string`| The URL of the document add to the advert.

Either `document` or `document_url` is required. If both are supplied, an error will be returned.

Here's a `curl` example:

    curl -i -H 'Authorization: Bearer 471dfba...' \
      -H 'Content-Type: multipart/form-data' \
      -F document=@size-guide.pdf \
      -X POST \
      https://bikeexchange.com.au/api/v2/client/adverts/1/documents

or

    curl -i -H 'Authorization: Bearer 471dfba...' \
      -F document_url=https://my.site/documents/size-guide.pdf \
      -X POST \
      https://bikeexchange.com.au/api/v2/client/adverts/1/documents

### Response

<div class="alert alert-success" role="alert">
  Status: 200 OK
</div>

```json
{
  data: {
    id: "1",
    type: "documents",
    attributes: {
      filename: "size-guide.pdf",
      path: "Ff/FFF-3Ff333fFffFFf3-F3FFfF.pdf",
      url: "https://marketplacer.imgix.net/Ff/FFF-3Ff333fFffFFf3-F3FFfF.pdf?s=11111111111111"
    }
  }
}
```


## Updating a document

To update a document, you must first know its position within the list of documents returned from the [listing documents](/v2/documents#listing-an-adverts-documents) endpoint.

### Request

    PUT /api/v2/client/adverts/1/documents/1

### Input

Name | Type | Description
-----|------|--------------
`document`|`file`| The document data to add to the advert.
`document_url`|`string`| The URL of the document add to the advert.

Either `document` or `document_url` is required. If both are supplied, an error will be returned.

Here's a `curl` example:

    curl -i -H 'Authorization: Bearer 471dfba...' \
      -H 'Content-Type: multipart/form-data' \
      -F document=@size-guide.pdf \
      -X PUT \
      https://bikeexchange.com.au/api/v2/client/adverts/1/documents/1
      
### Response

<div class="alert alert-success" role="alert">
  Status: 200 OK
</div>

```json
{
  data: {
    id: "1",
    type: "documents",
    attributes: {
      filename: "size-guide.pdf",
      path: "Ff/FFF-3Ff333fFffFFf3-F3FFfF.pdf",
      url: "https://marketplacer.imgix.net/Ff/FFF-3Ff333fFffFFf3-F3FFfF.pdf?s=11111111111111"
    }
  }
}
```

## Deleting a document

To update a document, you must first know its position within the list of documents returned from the [listing documents](/v2/documents#listing-an-adverts-documents) endpoint.

### Request

    DELETE /api/v2/client/adverts/1/documents/1

### Response

<div class="alert alert-success" role="alert">
  Status: 204 No Content
</div>

Note: The subsequent documents will move up in position. To delete all the documents you will need to request `DELETE /api/v2/client/adverts/1/documents/1` 4 times.