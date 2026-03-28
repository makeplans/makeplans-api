---
title: Resource Exception Dates
nav_order: 7
---

Opening hours for a resource is what is used to generate available slots for a service.
It is based on the default opening hours on the resource unless anything else is specified by a resource exception date.
So ignore the poor naming of the feature and treat it as opening hours on dates.
If a resource should be closed for a week then you would add resource exception dates.
If a resource should have opening hours 08:00 to 14:00 on January 5th 2016 instead of the default opening hours for that weekday which could be 09:00 to 15:00, then add it as a resource exception date.
See [resource opening hours](/endpoints/resources/#default-opening-hours) for how to specify.

A `null` value will fallback to the default availability for the resource. An empty array means the resource is closed on the specified exception date.

Exception dates are related to a resource: `GET /resources/{resource_id}/exception_dates`.

## Attributes

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>created_at</td><td>Datetime</td><td>Automatically set</td></tr>
  <tr><td>updated_at</td><td>Datetime</td><td>Automatically set</td></tr>
  <tr><td>resource_id</td><td>Integer</td><td>Required (specified in URL)</td></tr>
  <tr><td>exception_date</td><td>Date</td><td>Required (specified in URL)</td></tr>
  <tr><td>opening_hours</td><td>Array</td><td>Same format as resource opening hours</td></tr>
  <tr><td>service_id</td><td>Integer</td><td></td></tr>
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
</table>

## Get exception date

`GET /resources/{resource_id}/exception_dates/{exception_date}` will get the exception date `{exception_date}` for resource `{resource_id}`. If no existing entry is found a 404 HTTP status code is returned. In that case standard opening hours apply.

## Add new or update exception date

`POST /resources/{resource_id}/exception_dates/{exception_date}` will create a new or update an existing exception date `{exception_date}` for resource `{resource_id}`. You can also use `PUT`, both methods works the same way regardless if it is a new entry or an update to an existing one.

## Add new or update multiple exception dates

`POST /resources/{resource_id}/exception_dates/` will create new or update existing exception dates for resource `{resource_id}`. You can also use `PUT`, both methods works the same way regardless if it is a new entry or an update to an existing one.

To delete an existing exception date set `{_destroy}` to be true.

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

`DELETE /resources/{resource_id}/exception_dates/{exception_date}` will delete existing exception date `{exception_date}` for resource `{resource_id}`.
