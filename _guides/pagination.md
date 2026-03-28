---
title: Pagination and Examples
nav_order: 10
---

Maximum 50 items are returned per response for all endpoints except `bookings` which returns 750. Specify page with parameter `page`. Pagination is used for: bookings, events, people and resource exception dates. All other objects return all available items. Please note that currently there is no way to specify exact part of the dataset. Thus an item might appear in both page 2 and 3 if the complete dataset for the query has changed.

[RFC 8288](https://datatracker.ietf.org/doc/html/rfc8288){:target="_blank"} is used for pagination linking. You will find URLs for first, prev, next and last pages in the `Link` HTTP Header. In addition metadata is added in the following HTTP Headers: `Current-Page`, `Page-Items`, `Total-Count`, `Total-Pages`.

## Example request and response

```shell
curl -H 'Authorization: Bearer APIKEY' \
-H 'User-Agent: YourAppName' \
-H 'Accept: application/json' \
https://youraccount.test.makeplans.net/api/v1/services
```

To create something you have to make a POST:

```shell
curl -H 'Authorization: Bearer APIKEY' \
-H 'User-Agent: YourAppName' \
-H 'Accept: application/json' \
-d 'service[title]=My new service!&service[interval]=40' \
-X POST \
https://youraccount.test.makeplans.net/api/v1/services
```

[More examples.](/examples/overview/)
