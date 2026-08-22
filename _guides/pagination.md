---
title: Pagination and Examples
nav_order: 10
description: Pagination in the Makeplans API — page sizes, the page parameter and RFC 8288 Link headers.
---

Maximum 100 items are returned per response for all endpoints except `bookings` which returns 750. Specify page with parameter `page`. Pagination is used for: bookings, events, orders and people. All other objects return all available items. Please note that currently there is no way to specify exact part of the dataset. Thus an item might appear in both page 2 and 3 if the complete dataset for the query has changed.

[RFC 8288](https://datatracker.ietf.org/doc/html/rfc8288){:target="_blank"} is used for pagination linking. The `Link` HTTP header contains URLs with the relation types `first`, `previous`, `next` and `last`. `previous` and `next` are omitted on the first and last page respectively, so you can paginate by following `next` until it is absent. In addition metadata is added in the following HTTP Headers: `current-page`, `page-limit`, `total-count`, `total-pages`.

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
