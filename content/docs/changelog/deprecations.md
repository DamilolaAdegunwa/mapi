---
title: "Deprecations"
description: "Deprecation Policy"
date: 2020-11-12T13:26:54+01:00
lastmod: 2020-11-12T13:26:54+01:00
draft: false
images: []
menu:
  docs:
    parent: "changelog"
    identifier: "deprecations-changelog"
weight: 1510
toc: true
---

### Policy

From time to time we’ll want to deprecate, and ultimately remove, fields from our GraphQL API. 

Reasons for this can include, but are not limited to:

- Field is no longer used
- We have a better existing alternative
- Too complex / non-adherent to our API design principles.

Don’t worry though - we follow the following process to ensure we give everyone enough time to make the necessary changes.

### The Process

#### Identify

- We identify the fields we wish to deprecate
- Update the `deprecated_reason` for the relevant field in our GraphQL schema, which will include:
  - The final removal date (currently 6 months from identification)
  - An alternative field / approach that can be used

#### Notify

- We'll make reasonable attempts to notify the users of deprecated fields via email
- We'll maintain and update this page (which we recommend you check from time to time!)

#### Remove

Following the 6 month notification period, we ultimately remove the deprecated field, we will of course take into consideration whether there is still any significant traffic on the field prior to final removal.

### Deprecated Fields

The following fields have been marked for deprecation:

Object | Deprecated Field | Alternative | Scheduled Removal Date
---|---|---|---
`Advert` | `domesticShippingCost` | Use `baseDomesticShippingCost` instead. | 26 Oct 2021
`Advert` | `hasFreeInternationalPostage` | Use `hasFreeInternationalShipping` instead. | 26 Oct 2021
`Advert` | `hasFreePostage` | Use `hasFreeDomesticShipping` instead. | 26 Oct 2021
`Advert` | `internationalShippingCost` | Use `baseInternationalShippingCost` instead. | 26 Oct 2021
`OptionValue` | `presentation` | Use `displayName` instead. | 26 Oct 2021
`SocialLink` | `medium` | Use `socialNetwork` instead. | 26 Oct 2021

### Removed Fields

Object | Deprecated Field | Alternative | Removal Date
---|---|---|---
`Location` | `slug` | Use `locationSlug` | 28 Apr 2021
`SiteConfig` | `googleAnalyticsAccountId` | Use google_analytics_account_ids | 28 Apr 2021