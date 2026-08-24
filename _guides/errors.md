---
title: Errors
nav_order: 10
description: HTTP status codes and error responses from the Makeplans API, and how to handle them.
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

Authentication error. Returned when the request is not authenticated, for example if the API-Key is missing or invalid, or if the API is not enabled for the account. The response body does not specify the cause.

Example response:

```json
{
  "error": {
    "status": 1,
    "description": "not authenticated"
  }
}
```

## 402 - Payment Required

Please pay your bill.

## 403 - Forbidden

Authorization error. If you are authenticated but lack permissions.

## 404 - Not Found

Obviously incorrect paths (`/cats`) returns 404. However, even though cool URIs should not change, previously available objects, let's say `/resources/666`, might have been deleted and thus return a 404 when requested. In most cases deleted resources will be returned and have a state or a flag that indicates that the resource is inactive or deleted.

## 429 - Too Many Requests

You can perform up to 300 requests per 60 second period from the same IP address. If you exceed this limit, you'll get a 429 Too Many Requests response for subsequent requests. Check the `Retry-After` HTTP-header to see how many seconds to wait before retrying the request.

## 5xx - Server error

System errors (aka we screwed up) returns 5xx HTTP status codes without any detailed information. We log all system errors, but please contact us if you get this response.

```json
{
  "error": {
    "description": "System error"
  }
}
```
