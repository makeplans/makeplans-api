---
title: Example Requests
nav_order: 8
description: Example requests to the Makeplans API — reading data with GET and creating objects with POST.
---

To read data you make a GET request:

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

## Paginated request

[Paginated listings](/guide/pagination/) are requested with the `page` parameter:

```shell
curl -H 'Authorization: Bearer APIKEY' \
-H 'User-Agent: YourAppName' \
-H 'Accept: application/json' \
'https://youraccount.test.makeplans.net/api/v1/bookings?page=2'
```

Response headers (the `link` header is a single line, wrapped here for readability):

```
link: <https://youraccount.test.makeplans.net/api/v1/bookings?page=1>; rel="first",
      <https://youraccount.test.makeplans.net/api/v1/bookings?page=1>; rel="previous",
      <https://youraccount.test.makeplans.net/api/v1/bookings?page=3>; rel="next",
      <https://youraccount.test.makeplans.net/api/v1/bookings?page=4>; rel="last"
current-page: 2
page-limit: 750
total-count: 2861
total-pages: 4
```

[More examples.](/examples/overview/)
