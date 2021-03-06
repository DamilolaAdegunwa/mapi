---
title: "Getting Started"
description: "Getting started with fakeco webhooks"
date: 2020-11-12T13:26:54+01:00
lastmod: 2020-11-12T13:26:54+01:00
draft: false
images: []
menu:
  docs:
    parent: "webhooks"
    identifier: "getstarted-webhooks"
weight: 1210
toc: true
---

This guide takes you through how to:

- Set up a test HTTP POST endpoint
- Register a webhook in Marketplacer Admin
- Invoke the webhook

## Setup a test HTTP POST Endpoint

The webhook events generated by Marketplacer require a HTTP POST endpoint as the target destination. In a production environment, it would be the responsibility of the _Information Consumer_ to provide this endpoint, however for the purposes of this tutorial, we're going to set up a test endpoint using a, (free),  tool called [Pipedream](https://pipedream.com/).

**Note**: If you already have a HTTP endpoint that can receive POST requests via the internet, then you can choose to skip this section and move on to: 

{{< alert icon="👉" text="<b>Take Note: </b> If you already have a HTTP endpoint that can receive POST requests via the internet, then you can choose to skip this section and move on to: <b>Registering a Webhook</b>" />}}

Following sign-up and login, (not shown here!), at: https://pipedream.com navigate to **workflows**:

![Pipedream workflows](../images/pipedream_1.png)

<br/>

Select **New**

![Pipedream workflows](../images/pipedream_2.png)

<br/>

Then select **HTTP / Webhook** from the list of available triggers:

![Pipedream workflows](../images/pipedream_3.png)

<br/>

Choose the webhook type most suited to your needs, we'll go with **New Requests**

![Pipedream workflows](../images/pipedream_4.png)

<br/>

Accept the defaults on the next screen and select **Create Source**

![Pipedream workflows](../images/pipedream_5.png)

<br/>

This should create a HTTP endpoint that's available and listening for incoming HTTP POST (webhook event) requests. Make a note of the URL as we'll be using that in the next section.

![Pipedream workflows](../images/pipedream_6.png)

<br/>

## Registering a Webhook

Webhooks can be registered at both the Seller and / or Marketplace level, in this guide we show you how to access webhook management from both:

- The Seller Portal
- The Admin (Operator) Portal

Aside from the different entry points, the process for managing webhooks is the same.

<br/>

### Webhooks from the Seller Portal

- Login to the relevant Seller Portal
- Select API & Integrations
- Select Webhooks

![Webhooks Configuration](../images/admin_webhooks_0.png)

<br/>

This should then take you to **Webhook Management**, see below.

### Webhooks from the Admin Portal

Login to Marketplacer Admin:

![Marketplacer Admin](../images/admin_login.png)

<br/>

In the left hand menu, expand **Configuration** and select **Webhooks**:

![Webhooks Configuration](../images/admin_webhooks_1.png)

<br/>

This should then take you to **Webhook Management**, see below.

### Webhook Management

Webhook Management is where you would come to:

- Add new webhooks
- Edit existing webhooks
- Look at events generated on existing webhooks

<br/>

Selecting **New Webhook** takes you to the webhook creation form as shown below:

![Add a new webhook](../images/admin_webhooks_2.png)

<br/>

These sections are described in more detail below:

### Enabled

Self-explanatory really, but remember to tick if you want to generate webhooks - the default is un-ticked...

### Type

Select from 1 of three types:

- Advert (Product)
- Invoice
- Refund Request
- Shipment
- Variant (Product Variant)

In this example we'll select the **Invoice** type.

![Invoice Type Selected](../images/admin_webhooks_3.png)

<br/>

### Event Name (filter)

By default you will receive all events for your selected object e.g.:

- Create (Invoice is newly created)
- Update (Invoice has been updated)
- Returned (Refund Request has been processed))

Using the **Event Name** you can filter down to _just 1 event_ type, e.g. `create`. To do so, simply enter the name of the event you want to filter on, e.g. `create`. At this point in time you can only filter down to 1 event type, meaning you can't filter down to multiple selections of events such as `create` _**and**_ `update` etc...

It is suggested that if you want to use this feature that you first become familiar with all events that are generated (i.e. leave this field blank). Then by interrogating the value of `event_name` in the webhook payload, you can start to determine:

- What event(s) you really need
- The event name strings that you can used to filter

### URL

Use the URL of your HTTP POST endpoint, (we generated this using Pipedream in the last section):

![Invoice Type Selected](../images/admin_webhooks_4.png)

<br/>

### GraphQL

You have 2 options here:

1. Leave blank - we'll send you a basic payload for the entity of your choosing
2. Provide a GraphQL query - this allows you to tailor the payload returned - we describe this in more detail below

<br/>

#### Providing a GraphQL Query

As we have selected the `invoice` as our entity for this example, the GraphQL query below shows not only how you can return fields directly on an `invoice` but also for entities related to `invoice`:

- `order`
- `refundRequests`
- `seller`
- `shipment`
- `lineItems`

You'll also note that we make use a variable called `$id` in order that we can dynamically supply the ID value for our Invoice:

    query($id: ID!)
    {
        node(id: $id){
            ... on Invoice{
                id
                status
                order 
                { 
                    id 
                    status
                } 
                refundRequests 
                { 
                    cashAmountCents 
                    invoice 
                    { 
                        id 
                    } 
                } 
                seller 
                { 
                    id
                    businessName 
                } 
                shipments 
                { 
                    id 
                    carrier 
                    trackingLink 
                    trackingNumber 
                } 
                lineItems 
                { 
                    id 
                    status 
                }
            }
        }
    }

**Note:** A more detailed discussion on querying with GraphQL is outside the scope of this tutorial, for more information on the GraphQL query language, please refer to [graphql.org](https://graphql.org/)

Adding this query to our webhook registration, you should see something similar to the following:

<br/>

![GraphQL Query](../images/admin_webhooks_5.png)

<br/>

### Headers

Again this is an optional field, if you have a need to provide any further headers you can do so here using the following as an example:

`{"Entity":"Invoice"}`

### The Complete Registration

If you have followed the steps above, you should have something similar to this:

![Completed Registration Form](../images/admin_webhooks_6.png)

<br/>

Saving the webhook you should be returned to webhook management where you'll see your registered webhook, be sure to check that it is enabled (the green tick):

<br/>

![Registered Webhook](../images/admin_webhooks_7.png)

## Invoking the Webhook

As we have chosen an `invoice` as our entity, most "changes" to an `invoice` will invoke a webhook event, e.g.:

- Invoice creation
- Invoice payment status
- Shipment status

So the simplest of these would be to create a new `order` (which will generate an associated `invoice`). We'll use the Marketplacer storefront to do this as follows:

- Browse for an item
- Add item to cart
- Enter customer details (or login if you have an account)
- Enter payment details
- Complete Order (Click Pay Now)

For the purposes of brevity we won't detail each of these steps with a screenshot, instead jumping to just before completing the order:

<br/>

![Complete order](../images/admin_webhooks_8.png)

<br/>

Click "Pay Now" to generate your order and over in Pipedream we should see some events come through:

<br/>

![Webhook Events](../images/admin_webhooks_9.png)

<br/>

You'll note that we get 2 events:

1. Create
2. Update (when we acknowledge payment)

You'll also see that we get a JSON payload that matches the result of our GraphQL query.

<br/>

## Next Steps

The process for setting up webhooks for the other entity types is exactly the same, the only difference would be the GraphQL query should you choose to provide one.