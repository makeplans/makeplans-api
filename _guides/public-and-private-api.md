---
title: Public and Private API
nav_order: 2
---

If you are planning to integrate directly from your application to Makeplans or need to modify information in Makeplans then you must use the private API. This API requires authentication with an API-key and should not be done publicly (on a website).

The public API is available for simple read-only operations. This is useful if you want to present information from Makeplans on your website using JavaScript. Because these operations are done on a public website you can not authenticate using your API-key. With the public API you can fetch a list of services, available timeslots and initialize the booking process.

All requests are done over HTTPS. Each object in Makeplans has its own [endpoints](/endpoints/).

## Base API URL

### Private API

The base URL is `https://youraccount.makeplans.com/api/` for production apps and `https://youraccount.test.makeplans.net/api/` for test apps.

#### Versioning

The current version of the API is version 1. The versioning scheme is as follows: `/api/v{version_number}/`. All paths in the rest of the document uses `/api/v1/` as base path.

See the [API changelog](/changelog/) for improvements and changes.

### Public API

The base URL is your account booking site such as `https://youraccount.makeplans.com/`. There is no versioning. Only the latest version of the API is available.

Only read-only operations such as list and show are available with the public API. If you need to modify any data please use the private API.
