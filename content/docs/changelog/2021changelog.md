---
title: "2021 Changelog"
description: "Changes for 2021"
date: 2020-11-12T13:26:54+01:00
lastmod: 2020-11-12T13:26:54+01:00
draft: false
images: []
menu:
  docs:
    parent: "changelog"
    identifier: "2021-changelog"
weight: 1505
toc: true
---

Last Updated: 25th May 2021

### May 20 - 2021

![GraphQL Change](../images/GraphQL.png) 

#### Expose heapAnalyticsKey on ExternalAccountSettings

The `heapAnalyticsKey` field now exposed on the `ExternalAccountSettings` Object

For further information on the `ExternalAccountSettings` object, please refer to the docs [here](https://api.marketplacer.com/graphql/externalaccountsettings.doc.html).

<hr/>

### May 17 - 2021

![GraphQL Change](../images/GraphQL.png) 

#### Expose advertTitle and variantName on LineItem

At the point when a `LineItem` object is created we now capture & retain both:

- `advertTitle`: The advert (product) title
- `variantName`: The name of the variant

This means that even if the values of `advert.title` and `variant.name` change directly on those respective objects, the values of `advertTitle` and `variantName` on the `LineItem` object will remain as they were at the point of original capture.

<hr/>

### May 11 - 2021

![GraphQL Change](../images/GraphQL.png) 

#### Image Rendering - Users can supply a "fillColor" when using the Fill method

When rendering image URLs via GraphQL, users can specify a number of "fit" options that allow the user to tailor how the image is rendered. This change allows the users to supply an optional `fillColor` parameter when using the `fill` option as shown below:

```graphql
    url(width: 1440 height:1800 fit:fill fillColor: "FFFFFF")
```

In the above example images will be rendered at 1440 x 1800 with a maintained aspect-ratio, and any "redundant" space will be filled white.

<hr/>

![GraphQL Change](../images/GraphQL.png) 

#### Added Invoice to Shipments

Prior to this change you could obtain your shipments _from_ your invoice object via the `shipments` array. This change introduces a relationship back from the shipment to the invoice, by adding a (non nullable) `invoice` field to the `shipment` object. 

For further information on the `shipment` object, please refer to the docs [here](https://api.marketplacer.com/graphql/shipment.doc.html).

<hr/>

![GraphQL Change](../images/GraphQL.png) 

#### Added Condition, Gender and Availability Filter Options

The following Filter Options have been added to the applicable filters for the `advertSearch` query:

- `ConditionFilterOption` - refer to [docs here](https://api.marketplacer.com/graphql/conditionfilteroption.doc.html)
- `GenderFilterOption` - refer to [docs here](https://api.marketplacer.com/graphql/genderfilteroption.doc.html)
- `AvailabilityFilterOption` - refer to [docs here](https://api.marketplacer.com/graphql/availabilityfilteroption.doc.html)

The addition of these filters will allow consumers of `advertSearch` to further finesse their result sets. For more information on querying with GraphQL please refer to our [Getting Started](../../graphql/getting_started) guide.

<hr/>

### May 10 - 2021

![Webhook Change](../images/Webhook.png) 

#### Added dedicated Seller webhook

In addition to the existing webooks for the: `Advert`, `Invoice`, `RefundRequest` and `Variant` objects we have introduced near real-time event notifications on the `Shipment` object. You can now receive update events directly on this object as opposed to relying on parent objects such as `Invoice`.

For further details on working with our webhooks, please refer to the docs [here](../../webhooks/overview).

<hr/>

### April 30 - 2021

![GraphQL Change](../images/GraphQL.png) 

#### paymentInstrumentType Data Type Change

The `paymentInstrumentType` field on the `order` object has been changed from an ENUM to a String. This will allow for null responses on this field. Additionally this brings it into design alignment with `paymentInstrumentType` on the `PaymentEvent` object.

Traffic analyis of `paymentInstumentType` on `order` indicated that this would not impact production users.

For further details please refer to the `order` object documentation [here](https://api.marketplacer.com/graphql/order.doc.html).

<hr/>

### April 29 - 2021

![GraphQL Change](../images/GraphQL.png) 

#### Expose detail for Unpublished Adverts

Prior to this change, when querying `unpublishedAdverts` objects you would only receive a subset of fields (when compared with a published `Advert`). With this change we now expose the `details` field, which allows for the retrieval of all `Advert` fields. 

For further details please refer to the `UnpublishedAdvert` object documentation [here](https://api.marketplacer.com/graphql/unpublishedadvert.doc.html).

<hr/>

### April 28 - 2021

![GraphQL Change](../images/GraphQL.png) 

#### Remove slug and googleAnalyticsAccountId Fields

The following fields have now been permanently removed:

- `slug` field was removed from the `Location` object
- `googleAnalyticsAccountId` field was removed from the `SiteConfig` object

For further details on our Field Deprecation Policy, as well as the suggested alternatives for the removed fields, please refer to our [Deprecated Fields page](../deprecations)


<hr/>


### April 26 - 2021

![GraphQL Change](../images/GraphQL.png) 

#### Expose Database ID on LineItem Object

We have now exposed the following fields on the `LineItem` object:

- LegacyId

For further details please refer to the `LineItem` object documentation [here](https://api.marketplacer.com/graphql/lineitem.doc.html).

<hr/>

![GraphQL Change](../images/GraphQL.png) 

#### Expose Database ID on RefundRequestLineItem Object

We have now exposed the following fields on the `RefundRequestLineItem` object:

- LegacyId

For further details please refer to the `RefundRequestLineItem` object documentation [here](https://api.marketplacer.com/graphql/refundrequestlineitem.doc.html).

<hr/>


### April 20 - 2021

![GraphQL Change](../images/GraphQL.png) 

#### Additional fields now available on the LineItem object

We have now exposed the following fields on the `LineItem` object:

- totalCents
- totalFormatted
- subtotalCents
- subtotalFormatted
- discountCents
- discountFormatted
- taxTotalCents
- taxTotalFormatted

For further details please refer to the `LineItem` object documentation [here](https://api.marketplacer.com/graphql/lineitem.doc.html).

<hr/>

### April 19 - 2021

![GraphQL Change](../images/GraphQL.png) 

#### Exposed "Powered by" on the AccountSettings object

The `displayPoweredByText` field has been exposed on the `AccountSettings` object. This allows headless clients to determine if they'd like the "Powered by Marketplacer" (or equivalent) tag line displayed.

For further details please refer to the `AccountSettings` object documentation [here](https://api.marketplacer.com/graphql/accountsettings.doc.html).

<hr/>

### April 15 - 2021

![GraphQL Change](../images/GraphQL.png) 

#### Auto-calculate refund amount when using refundRequestCreate

If a refund request is created without supplying a refund amount, we now auto-calculate this from the Line Items and Quantities supplied.

If a refund request is created with a supplied refund amount and it does not match the calculation of the total from line items and quantities, we create an extra "adjustment" refund request line item that brings it to the supplied refund amount.

For further details on how refunding works via our GraphQL API, please refer to the documentation [here](https://api.marketplacer.com/howto/graphqlhowtorefund/).

<hr/>