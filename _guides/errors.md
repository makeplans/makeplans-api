---
title: Errors
nav_order: 9
---

4xx HTTP status codes means you made a mistake and you need to adjust your request.

## 400 - Bad Request

API usage error. This means you did something wrong and there should be a message in the body that explains it. Error message is related to specified resource. Fix it and try again.

Example response:

```json
{
  "title": [
  "is a required field. Cannot be empty"
  ]
}
```

## 401 - Unauthorized

Authentication error. Response body will give explanation if there is authorisation issue or if the API is not enabled.

## 402 - Payment Required

Please pay your bill.

## 403 - Forbidden

Authorization error. If you are authenticated but lack permissions.

## 404 - Not Found

Obviously incorrect paths (`/cats`) returns 404. However, even though cool URIs should not change, previously available objects, lets say `/resources/666`, might have been deleted and thus return a 404 when requested. In most cases deleted resources will be returned and have a booking state or a flag that indicate that the resource is inactive or deleted.

## 429 - Too Many Requests

You can perform up to 80 requests per 20 second period from the same IP address. If you exceed this limit, you'll get a 429 Too Many Requests response for subsequent requests. Check the `Retry-After` HTTP-header to see how many seconds to wait before retrying the request.

## 5xx - Server error

System errors (aka we screwed up) returns 5xx HTTP status codes without any detailed information. We log all system errors, but please contact us if you get this response.

```json
{
  "error": "system error"
}
```
