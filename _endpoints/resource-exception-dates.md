---
title: Resource Exception Dates
nav_order: 7
description: API reference for Makeplans resource exception dates — per-date opening hours overrides for resources.
---

Opening hours for a resource is what is used to generate available slots for a service.
It is based on the default opening hours on the resource unless anything else is specified by a resource exception date.
So ignore the poor naming of the feature and treat it as opening hours on dates.
If a resource should be closed for a week then you would add resource exception dates.
If a resource should have opening hours 08:00 to 14:00 on January 5th 2016 instead of the default opening hours for that weekday which could be 09:00 to 15:00, then add it as a resource exception date.
See [resource opening hours](/endpoints/resources/#default-opening-hours) for how to specify.

A `null` value will fallback to the default availability for the resource. An empty array means the resource is closed on the specified exception date.

Exception dates are related to a resource: `GET /resources/{resource_id}/exception_dates`.

## Exception dates for a provider

An exception date can also be scoped to a specific service on the resource by setting `service_id`. This defines the opening hours on that date for the [provider](/endpoints/providers/) (the resource and service combination).

Service scoped exception dates are only used to generate slots when the service has `availability_type` set to `provider`. For such services, availability on a date is the service scoped exception date, with fallback to the provider opening hours for that weekday. For services with `availability_type` set to `resource` (the default), availability is the resource exception date, with fallback to the resource opening hours, and service scoped exception dates are ignored.

An exception date without `service_id` applies to the resource. All endpoints below accept `service_id` to operate on service scoped entries.

## Attributes

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>created_at</td><td>Datetime</td><td>Automatically set</td></tr>
  <tr><td>updated_at</td><td>Datetime</td><td>Automatically set</td></tr>
  <tr><td>resource_id</td><td>Integer</td><td>Required (specified in URL)</td></tr>
  <tr><td>exception_date</td><td>Date</td><td>Required (specified in URL)</td></tr>
  <tr><td>opening_hours</td><td>Array</td><td>Same format as resource opening hours</td></tr>
  <tr><td>service_id</td><td>Integer</td><td>Not required. Scopes the exception date to a service provided by the resource. When not set the exception date applies to the resource.</td></tr>
</table>

## Listing

`GET /resources/{resource_id}/exception_dates` will return all exception dates for resource with id `{resource_id}`.

Response

```json
[
  {
    "resource_exception_date": {
      "created_at": "2012-09-20T15:34:16+02:00",
      "resource_id": 1,
      "exception_date": "2014-01-08",
      "opening_hours": ["08:00", "12:00", "12:30", "17:30"],
      "updated_at": "2012-09-20T15:34:16+02:00"
    }
  }
]
```

### Query Parameters

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>from</td><td>Date</td><td></td></tr>
  <tr><td>to</td><td>Date</td><td></td></tr>
  <tr><td>service_id</td><td>Integer</td><td>Return exception dates scoped to this service. When not set only resource exception dates are returned.</td></tr>
</table>

## Get exception date

`GET /resources/{resource_id}/exception_dates/{exception_date}` will get the exception date `{exception_date}` for resource `{resource_id}`. If no existing entry is found a 404 HTTP status code is returned. In that case standard opening hours apply.

Add `service_id` as a query parameter to get a service scoped exception date: `GET /resources/{resource_id}/exception_dates/{exception_date}?service_id={service_id}`.

## Add new or update exception date

`POST /resources/{resource_id}/exception_dates/{exception_date}` will create a new or update an existing exception date `{exception_date}` for resource `{resource_id}`. You can also use `PUT`, both methods works the same way regardless if it is a new entry or an update to an existing one.

```json
{
  "resource_exception_date": {
    "opening_hours": [
      "08:00",
      "14:00"
    ],
    "service_id": 1
  }
}
```

Set `service_id` to create or update a service scoped exception date. Leave it out to create or update the exception date for the resource.

## Add new or update multiple exception dates

`POST /resources/{resource_id}/exception_dates/` will create new or update existing exception dates for resource `{resource_id}`. You can also use `PUT`, both methods works the same way regardless if it is a new entry or an update to an existing one.

To delete an existing exception date set `{_destroy}` to be true. Each entry can also set `service_id` to operate on a service scoped exception date.

```json
{
  "resource_exception_dates": [
    {
      "exception_date": "2015-10-22",
      "opening_hours": [
      "12:00",
      "14:30"
      ]
    },
    {
      "exception_date": "2015-10-25",
      "opening_hours": [
      "08:30",
      "12:30",
      "15:00",
      "18:30"
      ]
    },
    {
      "exception_date": "2015-10-26",
      "opening_hours": [
      "10:30",
      "15:00"
      ]
    },
    {
      "exception_date": "2015-10-27",
      "opening_hours": null
    },
    {
      "exception_date": "2015-10-28",
      "_destroy": true
    }
  ]
}
```

## Delete exception date

`DELETE /resources/{resource_id}/exception_dates/{exception_date}` will delete existing exception date `{exception_date}` for resource `{resource_id}`. Add `service_id` as a query parameter to delete a service scoped exception date.
