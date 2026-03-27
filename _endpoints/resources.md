---
title: Resources
nav_order: 6
---

## Attributes

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>id</td><td>Integer</td><td>Automatically set</td></tr>
  <tr><td>created_at</td><td>Datetime</td><td>Automatically set</td></tr>
  <tr><td>updated_at</td><td>Datetime</td><td>Automatically set</td></tr>
  <tr><td>title</td><td>String</td><td>Required</td></tr>
  <tr><td>active</td><td>Boolean</td><td>Automatically set</td></tr>
  <tr><td>opening_hours_mon</td><td>Array</td><td>Default opening hours for Monday.</td></tr>
  <tr><td>opening_hours_tue</td><td>Array</td><td>Default opening hours for Tuesday.</td></tr>
  <tr><td>opening_hours_wed</td><td>Array</td><td>Default opening hours for Wednesday.</td></tr>
  <tr><td>opening_hours_thu</td><td>Array</td><td>Default opening hours for Thursday.</td></tr>
  <tr><td>opening_hours_fri</td><td>Array</td><td>Default opening hours for Friday.</td></tr>
  <tr><td>opening_hours_sat</td><td>Array</td><td>Default opening hours for Saturday.</td></tr>
  <tr><td>opening_hours_sun</td><td>Array</td><td>Default opening hours for Sunday.</td></tr>
</table>

### Default opening hours

Values for the opening hours attributes is of type time in the array. Values are in the form of two's. This results in adding breaks within a day. To define opening hours from 8AM to 4PM with lunch at 12PM to 12.30PM the following array will be the result: `['08:00', '12:00', '12:30', '16:00']`. Having opening hours without a lunch break will yield this result: `['08:00', '16:00']`. To define a weekday as closed the value should be `null`.

### Deprecated Attributes

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>open_0</td><td>Time</td><td>Opening time for Monday</td></tr>
  <tr><td>open_1</td><td>Time</td><td>Opening time for Tuesday</td></tr>
  <tr><td>open_2</td><td>Time</td><td>Opening time for Wednesday</td></tr>
  <tr><td>open_3</td><td>Time</td><td>Opening time for Thursday</td></tr>
  <tr><td>open_4</td><td>Time</td><td>Opening time for Friday</td></tr>
  <tr><td>open_5</td><td>Time</td><td>Opening time for Saturday</td></tr>
  <tr><td>open_6</td><td>Time</td><td>Opening time for Sunday</td></tr>
  <tr><td>close_0</td><td>Time</td><td>Closing time for Monday</td></tr>
  <tr><td>close_1</td><td>Time</td><td>Closing time for Tuesday</td></tr>
  <tr><td>close_2</td><td>Time</td><td>Closing time for Wednesday</td></tr>
  <tr><td>close_3</td><td>Time</td><td>Closing time for Thursday</td></tr>
  <tr><td>close_4</td><td>Time</td><td>Closing time for Friday</td></tr>
  <tr><td>close_5</td><td>Time</td><td>Closing time for Saturday</td></tr>
  <tr><td>close_6</td><td>Time</td><td>Closing time for Sunday</td></tr>
</table>

These deprecated attributes define opening hours without breaks. Given `opening_hours_tue` with a break defined as this: `['08:00', '12:00', '12:30', '16:00']` it would yield `open_1` to be `08:00` and `close_1'` to be `16:00`. These attributes are deprecated and will be removed in a future API version.

## Listing

`GET /resources/` will return all resources.

Response

```json
[
  {
    "resource": {
      "capacity": 1,
      "created_at": "2012-09-20T15:34:16+02:00",
      "id": 1,
      "opening_hours_mon": ["08:00", "16:00"],
      "opening_hours_tue": ["08:00", "11:00", "13:00", "17:30"],
      "opening_hours_wed": ["08:00", "16:00"],
      "opening_hours_thu": ["08:00", "12:00", "14:00", "20:00"],
      "opening_hours_fri": ["08:00", "12:00", "12:30", "17:30"],
      "opening_hours_sat": null,
      "opening_hours_sun": null,
      "title": "Mr. Spine Twister",
      "updated_at": "2012-09-20T15:34:16+02:00"
    }
  }
]
```

## Get resource

`GET /resources/{resource_id}` will get a resource with id `{resource_id}`.

## Add new resource

`POST /resources` will create a new resource.

## Update resource

`PUT /resources/{resource_id}` will update existing resource with id `{resource_id}`.

## Delete resource

`DELETE /resources/{resource_id}` will delete existing resource with id `{resource_id}`. Deleting a resource will set it to active=false and will not be returned in any listings.

## Get opening hours for specific dates

Opening hours for specific dates is based on the standard opening hours that are specified on the resource but can be overridden with entries in [resource exception dates](/endpoints/resource-exception-dates/).

### Listing

`GET /resources/{resource_id}/opening_hours` will get opening hours on specific dates for resource with id `{resource_id}`.

Response

```json
[
  {
    "resource_opening_hours": {
      "date": "2015-12-03",
      "opening_hours": ["10:00", "12:00"]
    }
  },
  {
    "resource_opening_hours": {
      "date": "2015-12-04",
      "opening_hours": null
    }
  }
]
```

### Query Parameters

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>from</td><td>Date</td><td>Default: today</td></tr>
  <tr><td>to</td><td>Date</td><td>Default: today</td></tr>
</table>
