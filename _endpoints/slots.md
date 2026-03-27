---
title: Slots
nav_order: 1
---

Slots are not physical objects in Makeplans. It is a virtual representation of available times based on attributes from resources and services as well as various settings. So if a resource is open 8am to 4pm and selected service has interval of 60 minutes, slots will return an array of all time intervals (8am-9am, 9am-10am etc.) and indicate which resources are available.

Slots are meant for listing available times on the Makeplans booking page. You can however make bookings at any time and with any length - as long as the resource is available off course.

## Attributes

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>timestamp</td><td>DateTime</td><td>Start</td></tr>
  <tr><td>timestamp_end</td><td>DateTime</td><td>End</td></tr>
  <tr><td>formatted_timestamp</td><td>String</td><td>Localised human readable format</td></tr>
  <tr><td>formatted_timestamp_end</td><td>String</td><td>Localised human readable format</td></tr>
  <tr><td>free</td><td>Integer</td><td>Free capacity</td></tr>
  <tr><td>available_resources</td><td>Array</td><td>Ids of resources with availability</td></tr>
  <tr><td>maximum_capacity</td><td>Integer</td><td>The maximum capacity of the available resources</td></tr>
</table>

## Listing

`GET /services/{service_id}/slots` will return slots for specified service.

Response

```json
[
  {
    "slot": {
      "timestamp": "2013-03-08T10:00:00+00:00",
      "timestamp_end": "2013-03-08T10:15:00+00:00",
      "formatted_timestamp": "Friday, March  8, 2013, 10:00 AM",
      "formatted_timestamp_end": "Friday, March  8, 2013, 10:15 AM",
      "free": 1,
      "available_resources": [
        1,2
      ],
      "maximum_capacity": 2
    }
  }
]
```

### Query Parameters

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>from</td><td>Date</td><td>Default: today</td></tr>
  <tr><td>to</td><td>Date</td><td>Default: today</td></tr>
  <tr><td>selected_resources</td><td>Array</td><td>Default: all active providers.</td></tr>
</table>

## Next available date

`GET /services/{service_id}/next_available_date` will return the next available date within 30 days with a free slot.

Response

```json
[
  {
    "available_date": "2016-02-20"
  }
]
```

### Query Parameters

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>from</td><td>Date</td><td>Default: today.</td></tr>
  <tr><td>selected_resources</td><td>Array</td><td>Default: all active providers.</td></tr>
</table>

## Available dates

`GET /services/{service_id}/available_dates` will return the available dates for the rest of the current month.

Response

```json
[
  {
    "available_date": {
      "date": "2021-04-01"
    }
  },
  {
    "available_date": {
      "date": "2021-04-04"
    }
  }
]
```

### Query Parameters

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>from</td><td>Date</td><td>Default: today.</td></tr>
  <tr><td>to</td><td>Date</td><td>Default: rest of the month.</td></tr>
  <tr><td>selected_resources</td><td>Array</td><td>Default: all active providers.</td></tr>
</table>
