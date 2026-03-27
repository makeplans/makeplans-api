---
title: Providers
nav_order: 8
---

Resources provides services. This link is called a provider.

## Attributes

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>id</td><td>Integer</td><td>Automatically set</td></tr>
  <tr><td>created_at</td><td>Datetime</td><td>Automatically set</td></tr>
  <tr><td>updated_at</td><td>Datetime</td><td>Automatically set</td></tr>
  <tr><td>active</td><td>Boolean</td><td>Automatically set</td></tr>
  <tr><td>resource_id</td><td>Integer</td><td>Required</td></tr>
  <tr><td>service_id</td><td>Integer</td><td>Required</td></tr>
  <tr><td>opening_hours_mon</td><td>Array</td><td>Opening hours for Monday. Fallback to resource availability.</td></tr>
  <tr><td>opening_hours_tue</td><td>Array</td><td>Opening hours for Tuesday. Fallback to resource availability.</td></tr>
  <tr><td>opening_hours_wed</td><td>Array</td><td>Opening hours for Wednesday. Fallback to resource availability.</td></tr>
  <tr><td>opening_hours_thu</td><td>Array</td><td>Opening hours for Thursday. Fallback to resource availability.</td></tr>
  <tr><td>opening_hours_fri</td><td>Array</td><td>Opening hours for Friday. Fallback to resource availability.</td></tr>
  <tr><td>opening_hours_sat</td><td>Array</td><td>Opening hours for Saturday. Fallback to resource availability.</td></tr>
  <tr><td>opening_hours_sun</td><td>Array</td><td>Opening hours for Sunday. Fallback to resource availability.</td></tr>
</table>

Opening hours for a provider works in the same way as availability is defined on [resource exception date](/endpoints/resource-exception-dates/).

## Listing

`GET /providers/` will return all providers.

Response

```json
[
  {
    "provider": {
      "created_at": "2012-09-20T15:34:16+02:00",
      "id": 1,
      "resource_id": 1,
      "service_id": 1,
      "updated_at": "2012-09-20T15:34:16+02:00"
    }
  }
]
```

## Get provider

`GET /providers/{provider_id}` will get a provider with id `{provider_id}`.

## Add new provider

`POST /providers` will create a new provider.

## Delete provider

`DELETE /providers/{provider_id}` will delete existing provider with id `{provider_id}`.
