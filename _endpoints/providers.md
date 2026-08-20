---
title: Providers
nav_order: 8
description: API reference for the Makeplans providers endpoint — the link between resources and the services they provide.
---

Resources provides services. This link is called a provider.

## Attributes

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>id</td><td>Integer</td><td>Automatically set</td></tr>
  <tr><td>created_at</td><td>Datetime</td><td>Automatically set</td></tr>
  <tr><td>updated_at</td><td>Datetime</td><td>Automatically set</td></tr>
  <tr><td>resource_id</td><td>Integer</td><td>Required</td></tr>
  <tr><td>service_id</td><td>Integer</td><td>Required</td></tr>
  <tr><td>opening_hours_mon</td><td>Array</td><td>Opening hours for Monday.</td></tr>
  <tr><td>opening_hours_tue</td><td>Array</td><td>Opening hours for Tuesday.</td></tr>
  <tr><td>opening_hours_wed</td><td>Array</td><td>Opening hours for Wednesday.</td></tr>
  <tr><td>opening_hours_thu</td><td>Array</td><td>Opening hours for Thursday.</td></tr>
  <tr><td>opening_hours_fri</td><td>Array</td><td>Opening hours for Friday.</td></tr>
  <tr><td>opening_hours_sat</td><td>Array</td><td>Opening hours for Saturday.</td></tr>
  <tr><td>opening_hours_sun</td><td>Array</td><td>Opening hours for Sunday.</td></tr>
  <tr><td>priority_value</td><td>Integer</td><td>Not required. Priority value for ordering.</td></tr>
</table>

The opening hours format is the same as on [resources](/endpoints/resources/#default-opening-hours).

Provider opening hours are only used to generate slots when the service has `availability_type` set to `provider`. A `null` value for a weekday then means the provider is closed on that weekday. When the service has `availability_type` set to `resource` (the default), the opening hours on the resource are used and the provider opening hours are ignored.

Opening hours on specific dates for a provider are set with a service scoped [resource exception date](/endpoints/resource-exception-dates/#exception-dates-for-a-provider).

## Listing

`GET /providers` will return all providers for active resources.

Response

```json
[
  {
    "provider": {
      "created_at": "2012-09-20T15:34:16+02:00",
      "id": 1,
      "resource_id": 1,
      "service_id": 1,
      "opening_hours_mon": null,
      "opening_hours_tue": null,
      "opening_hours_wed": null,
      "opening_hours_thu": null,
      "opening_hours_fri": null,
      "opening_hours_sat": null,
      "opening_hours_sun": null,
      "priority_value": null,
      "updated_at": "2012-09-20T15:34:16+02:00"
    }
  }
]
```

## Get provider

`GET /providers/{provider_id}` will get a provider with id `{provider_id}`.

## Get opening hours for provider

`GET /providers/{provider_id}/opening_hours` will return the calculated opening hours per date for a provider, including any exception dates.

### Query Parameters

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>from</td><td>Date</td><td>Default: today</td></tr>
  <tr><td>to</td><td>Date</td><td>Default: same as from</td></tr>
</table>

Response

```json
[
  {
    "resource_opening_hours": {
      "date": "2015-10-22",
      "opening_hours": ["08:00", "14:00"]
    }
  }
]
```

## Add new provider

`POST /providers` will create a new provider.

## Update provider

`PUT /providers/{provider_id}` will update existing provider with id `{provider_id}`.

## Delete provider

`DELETE /providers/{provider_id}` will delete existing provider with id `{provider_id}`.
