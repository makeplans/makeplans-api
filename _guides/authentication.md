---
title: Authentication
nav_order: 6
---

Only the private API requires authentication. If you intend to use the API on a website please use the [public API](/guide/public-and-private-api/) which does not require authentication.

Use your account API-Key to authenticate with the Makeplans API. First enable the API on the account and you will find the API-Key in the account settings.

Send the API-Key as a Bearer token using the HTTP Authorization header.

Example: `Authorization: Bearer APIKEY`

Deprecated: For Basic Auth use the API-Key as username and empty password.

If your application is installable by end-users you should use oAuth. However we do not yet support oAuth so please contact us if this is something you require.
