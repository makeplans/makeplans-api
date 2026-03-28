---
title: Events
nav_order: 5
---

Unlike appointments made through a normal service events starts and ends at a specific time. An event is connected to a resource and a service. It could be either a one-off event (e.g. a concert) or something that occurs multiple times (e.g. spinning class). Event bookings have a strong relation to the event. That means that it is not possible to modify details such as `{booked_from}`, `{booked_to}`, `{resource_id}` and `{service_id}` for the `{booking}`. To make such changes it must be done to the event. All bookings connected to the event will then automatically be modified.

While events are connected to a resource bookings or capacity of an event are not restricted by the opening hours or availability of a resource.

## Attributes

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>id</td><td>Integer</td><td>Automatically set</td></tr>
  <tr><td>created_at</td><td>Datetime</td><td>Automatically set</td></tr>
  <tr><td>updated_at</td><td>Datetime</td><td>Automatically set</td></tr>
  <tr><td>resource_id</td><td>Integer</td><td>Required</td></tr>
  <tr><td>service_id</td><td>Integer</td><td>Required</td></tr>
  <tr><td>published</td><td>Boolean</td><td>Default: true</td></tr>
  <tr><td>capacity</td><td>Integer</td><td>Required</td></tr>
  <tr><td>starts_at</td><td>Datetime</td><td>Required</td></tr>
  <tr><td>ends_at</td><td>Datetime</td><td>Required</td></tr>
  <tr><td>custom_data</td><td>Array</td><td>Not required</td></tr>
  <tr><td>title</td><td>String</td><td></td></tr>
  <tr><td>description</td><td>Text</td><td></td></tr>
  <tr><td>first_booking_at</td><td>Datetime</td><td>Not required. Earliest allowed booking time.</td></tr>
  <tr><td>last_booking_at</td><td>Datetime</td><td>Not required. Latest allowed booking time.</td></tr>
  <tr><td>availability</td><td>Integer</td><td>Only for output. Current available capacity.</td></tr>
  <tr><td>nr_of_attendances</td><td>Integer</td><td>Only for output. Number of current attendees.</td></tr>
</table>

## Listing

`GET /events` will return all events.

Response

```json
[
  {
    "event": {
      "capacity": 10,
      "created_at": "2012-09-20T15:34:16+02:00",
      "custom_data": null,
      "description": null,
      "ends_at": "2015-08-10T11:30:00+02:00",
      "id": 1,
      "resource_id": 1,
      "published": true,
      "starts_at": "2015-08-10T10:00:00+02:00",
      "service_id": 1,
      "title": "Super fun event",
      "updated_at": "2012-09-20T15:34:16+02:00"
    }
  }
]
```

### Query Parameters

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>service_id</td><td>Integer</td><td></td></tr>
  <tr><td>resource_id</td><td>Integer</td><td></td></tr>
  <tr><td>start</td><td>Datetime</td><td>starts_at after param</td></tr>
  <tr><td>end</td><td>Datetime</td><td>ends_at before param</td></tr>
  <tr><td>since</td><td>Datetime</td><td>updated_at after param</td></tr>
</table>

## Get event

`GET /events/{event_id}` will get an event with id `{event_id}`.

## Add new event

`POST /events` will create a new event.

## Add recurring/multiple events

`POST /events/recurring` will create multiple events.

The recurrence format follows the [iCalendar specification](https://tools.ietf.org/html/rfc5545){:target="_blank"}. The attributes for recurrence are: `RRULE`, `RDATE`, `EXDATE`. For an introduction and examples of these parameters see [this section from the iCalendar specification](http://www.kanzaki.com/docs/ical/rrule.html){:target="_blank"}.

In the iCalendar specification the recurrence is based on values in `DTSTART` and `DTEND`. This is set by `starts_at` and `ends_at` from `event`.

All recurring events will except for `starts_at` and `ends_at` have the same attributes based on the specified parameters in `event`.

All events created by the recurring pattern gets the same UUID in `collection_id`. As the collection name implies, and as is possible with the iCalendar specification, this is not necessarily only for recurrence (i.e. 9am-10am each Friday until December 1st) but also for multiple specific times (as can be specified with `RDATE`).

Only the `collection_id` is returned. No events are created at the time of request as they will be processed by the server in the background due to the volume of events that it is possible to create at one time. A successful response with a `collection_id` does in no way indicate that any events will be created. The first event as defined in `event` is however validated. If it is not valid errors details are returned in the same way as creating a single event. In such a case recurring rules are not applied and you must adjust the request until validation is successful.

### Parameters for recurrence

The parameters for recurrence are not set in `event` but in `recurrence`.

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>rrule</td><td>String</td><td>Repeating pattern. Example: `FREQ=DAILY;UNTIL=19971224T000000Z`.</td></tr>
  <tr><td>rdate</td><td>String</td><td>List of recurring dates. Example: `VALUE=DATE:19970101,19970120,19970217,19970421`.</td></tr>
  <tr><td>exdate</td><td>String</td><td>List of dates that should be excluded from the recurring rule. Example: `VALUE=DATE:19970102`.</td></tr>
</table>

You should always specify COUNT or UNTIL with RRULE. The max number of occurrences is 731, regardless if a limit is set or not.

### List occurrences

`GET /events/recurring/{collection_id}` will return all occurrences for a collection.

## Update event

`PUT /events/{event_id}` will update existing event with id `{event_id}`.

## Delete event

`DELETE /events/{event_id}` will delete existing event with id `{event_id}`. Deleting an event will set it to active=false and will not be returned in any listings.

## List bookings

`GET /events/{event_id}/bookings` will return all bookings for event with id `{event_id}`.
