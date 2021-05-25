---
title: "Images"
description: "Here we describe the Seller API Image resource."
date: 2020-11-12T13:26:54+01:00
lastmod: 2020-11-12T13:26:54+01:00
draft: false
images: []
menu:
  docs:
    parent: "sellerapi"
    identifier: "images-sellerapi"
weight: 950
toc: true
---

Images are displayed prominently on an [advert](/docs/sellerapi/adverts)'s page. An advert can have up to 20 images. Images can also be assigned to individual advert [variants](/v2/variants)


## Listing an advert or variant's images

### Request

    GET /api/v2/client/adverts/1/images

OR

    GET /api/v2/client/variants/1/images

### Response

<div class="alert alert-success" role="alert">
  Status: 200 OK
</div>

```json
{
  "data": [
    {
      "id": "dea995bf53594ef2a754b7b14cd49e87",
      "type": "images",
      "attributes": {
        "url": "/dbimages/bike/fn_large/1/1/Cypress-DX.jpg"
      }
    }
  ]
}
```

## Showing a single image

This endpoint allows you to request an image at a specific position within the advert or variant's list of images. The example request here shows how to get the first image. Numbers up to 20 are supported.

### Request

    GET /api/v2/client/adverts/1/images/1

OR

    GET /api/v2/client/variants/1/images/1

### Response

<div class="alert alert-success" role="alert">
  Status: 200 OK
</div>

```json
{
  data: {
    id: "dea995bf53594ef2a754b7b14cd49e87",
    type: "images",
    attributes: {
      url: "/dbimages/bike/fn_large/1/1/Cypress-DX.jpg"
    }
  }
}
```

## Adding an image

This endpoint allows you to add up to 20 images to an advert or variant. For image uploads you may need to set the Content-Type to be `multipart/form-data`.

### Request

    POST /api/v2/client/adverts/1/images

OR

    POST /api/v2/client/variants/1/images

### Input

Name | Type | Description
-----|------|--------------
`image`|`file`| The image data to add to the advert.
`image_url`|`string`| The URL of the image add to the advert.

Either `image` or `image_url` is required. If both are supplied, an error will be returned.

Here's a `curl` example:

    curl -i -H 'Authorization: Bearer 471dfba...' \
      -H 'Content-Type: multipart/form-data' \
      -F image=@bike.jpg \
      -X POST \
      https://bikeexchange.com.au/api/v2/client/adverts/1/images

or

    curl -i -H 'Authorization: Bearer 471dfba...' \
      -F image_url=https://my.site/images/my_image.jpg \
      -X POST \
      https://bikeexchange.com.au/api/v2/client/adverts/1/images

### Response

<div class="alert alert-success" role="alert">
  Status: 200 OK
</div>

```json
{
  data: {
    id: "dea995bf53594ef2a754b7b14cd49e87",
    type: "images",
    attributes: {
      url: "/dbimages/bike/fn_large/1/1/Cypress-DX.jpg"
    }
  }
}
```

### Response (422)

If the destination of the image url is not available etc. you can expect to receive a `422 Unprocessable Entity` http response.

<div class="alert alert-danger" role="alert">
  Status: 422 Unprocessable Entity
</div>

```json
{
    {
      "errors": [
        {
          "title": "Unable to fetch image",
          "detail": "Unable to process the image url: Unable to retrieve attachment: https://badurl.com/corrupt.jp",
          "id": null,
          "href": null,
          "code": 422,
          "source": null,
          "status": "server_error"
        }
      ]
    }
}
```

## Updating an image

To update an image, you must first know its position within the list of images returned from the [listing images](/v2/images#listing-an-advert-or-variants-images) endpoint.

### Request

    PUT /api/v2/client/adverts/1/images/1

OR

    PUT /api/v2/client/variants/1/images/1

### Input

Name | Type | Description
-----|------|--------------
`image`|`file`| The image data to add.
`image_url`|`string`| The URL of the image add.

Either `image` or `image_url` is required. If both are supplied, an error will be returned.

Here's a `curl` example:

    curl -i -H 'Authorization: Bearer 471dfba...' \
      -H 'Content-Type: multipart/form-data' \
      -F image=@bike.jpg \
      -X PUT \
      https://bikeexchange.com.au/api/v2/client/adverts/1/images/1
      
### Response

<div class="alert alert-success" role="alert">
  Status: 200 OK
</div>

```json
{
  data: {
    id: "dea995bf53594ef2a754b7b14cd49e87",
    type: "images",
    attributes: {
      url: "/dbimages/bike/fn_large/1/1/Cypress-DX.jpg"
    }
  }
}
```

## Deleting an image

To update an image, you must first know its position within the list of images returned from the [listing images](/v2/images#listing-an-advert-or-variants-images) endpoint.

### Request

    DELETE /api/v2/client/adverts/1/images/1

OR

    DELETE /api/v2/client/variants/1/images/1

### Response

<div class="alert alert-success" role="alert">
  Status: 204 No Content
</div>

Note: The subsequent images will move up in position. To delete all the images you will need to request `DELETE /api/v2/client/adverts/1/images/1` 4 times.