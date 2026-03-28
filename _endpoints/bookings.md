---
title: Bookings
nav_order: 2
---

## Attributes

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>id</td><td>Integer</td><td>Automatically set</td></tr>
  <tr><td>created_at</td><td>Datetime</td><td>Automatically set</td></tr>
  <tr><td>updated_at</td><td>Datetime</td><td>Automatically set</td></tr>
  <tr><td>service_id</td><td>Integer</td><td>Not required</td></tr>
  <tr><td>event_id</td><td>Integer</td><td>Not required</td></tr>
  <tr><td>resource_id</td><td>Integer</td><td>Required</td></tr>
  <tr><td>person_id</td><td>Integer</td><td>Not required</td></tr>
  <tr><td>booked_from</td><td>Datetime</td><td>Required</td></tr>
  <tr><td>booked_to</td><td>Datetime</td><td>Required</td></tr>
  <tr><td>expires_at</td><td>Datetime</td><td>Not required</td></tr>
  <tr><td>title</td><td>String</td><td>Not required</td></tr>
  <tr><td>notes</td><td>Text</td><td>Not required</td></tr>
  <tr><td>reminder_at</td><td>Datetime</td><td>Not required</td></tr>
  <tr><td>reminded_at</td><td>Datetime</td><td>Not required</td></tr>
  <tr><td>paid_at</td><td>Datetime</td><td>Not required</td></tr>
  <tr><td>external_id</td><td>String</td><td>Not required</td></tr>
  <tr><td>paid_amount</td><td>Decimal</td><td>Not required</td></tr>
  <tr><td>revision_count</td><td>Integer</td><td>Automatically set</td></tr>
  <tr><td>created_by</td><td>String</td><td>Automatically set</td></tr>
  <tr><td>updated_by</td><td>String</td><td>Automatically set</td></tr>
  <tr><td>external_url</td><td>String</td><td>Not required</td></tr>
  <tr><td>external_host_url</td><td>String</td><td>Not required</td></tr>
  <tr><td>booked_by_person_id</td><td>Integer</td><td>Not required. Person who made the booking (e.g. parent booking for child).</td></tr>
  <tr><td>verification_method</td><td>String</td><td>Only for output.</td></tr>
  <tr><td>location_url</td><td>String</td><td>Only for output.</td></tr>
  <tr><td>location_host_url</td><td>String</td><td>Only for output.</td></tr>
  <tr><td>custom_data</td><td>Array</td><td>Not required</td></tr>
  <tr><td>state</td><td>String</td><td>Automatically set. See states</td></tr>
  <tr><td>active</td><td>Boolean</td><td>Automatically set</td></tr>
  <tr><td>count</td><td>Integer</td><td>Default: 1</td></tr>
  <tr><td>collection_id</td><td>UUID</td><td>Automatically set for recurring bookings.</td></tr>
  <tr><td>status</td><td>String</td><td>Default: null. See statuses.</td></tr>
  <tr><td>person_attributes</td><td>Object</td><td>See <a href="/endpoints/people/">person</a>. Only for input.</td></tr>
  <tr><td>person</td><td>Object</td><td>See <a href="/endpoints/people/">person</a>. Only for output.</td></tr>
  <tr><td>resource</td><td>Object</td><td>See <a href="/endpoints/resources/">resource</a>. Only for output.</td></tr>
  <tr><td>service</td><td>Object</td><td>See <a href="/endpoints/services/">service</a>. Only for output.</td></tr>
  <tr><td>event</td><td>Object</td><td>See <a href="/endpoints/events/">event</a>. Only for output.</td></tr>
  <tr><td>booking_type</td><td>String</td><td>Values: appointment or attendance. See <a href="/endpoints/services/">service</a>.</td></tr>
  <tr><td>coupon_id</td><td>Integer</td><td>Not required</td></tr>
</table>

## States

* awaiting_verification
* verification_expired
* awaiting_payment
* awaiting_confirmation
* confirmed
* declined
* cancelled
* deleted

## Statuses

* checked_in
* in_progress
* completed
* no_show

### Normal state flow

The normal booking flow when a customer initiates a new booking starts with `awaiting_verification`. If verification is required Makeplans sends out email or SMS for the customer to verify. When successfully verified the state is updated to `confirmed` or `awaiting_confirmation` if reservations requires confirmation by administrator. For confirmation it is then updated to `confirmed` as normally if confirmed, and to `declined` if it is not confirmed.

Bookings that have been confirmed and then cancelled, either by customer or administrator, are set to `cancelled`.

Bookings that are deleted are set to `deleted`.

## Active bookings

Bookings with states `awaiting_verification`, `awaiting_payment`, `awaiting_confirmation` or `confirmed` are considered to be active. Bookings with state `awaiting_verification` will be updated with state `verification_expired` after the current time passes `expires_at`. However updating states rely on automatic tasks so you must use the `active` attribute to check whether a booking is active or not. Only active bookings will be returned unless you specify: a booking by id, a specific state such as bookings that are awaiting confirmation, to return all bookings for a resource or dates, or for a person.

## Listing

See query parameters for filtering the output beyond the default outputs.

`GET /bookings` will return all active bookings.

`GET /bookings/recent` will return all active bookings ordered based on updated_at.

`GET /bookings/upcoming` will return all future active bookings.

`GET /bookings/unconfirmed` will return only unconfirmed bookings.

`GET /bookings/all` will return all bookings of all states (including `declined`, `cancelled`, `deleted`, and `verification_expired`). This is a useful output for synchronisation when you need to keep a track of deleted bookings.

`GET /bookings/visible` will return all active bookings as well as those declined or cancelled. This is the preferred output if you want to provide a list of all bookings for visual presentation.

`GET /bookings/no_status` will return all active bookings with no status set.

Response

```json
[
  {
    "booking": {
      "booked_from": "2012-09-29T07:00:00+02:00",
      "booked_to": "2012-09-29T08:00:00+02:00",
      "created_at": "2012-09-20T15:34:16+02:00",
      "custom_data": null,
      "count": 1,
      "expires_at": null,
      "external_id": null,
      "id": 1,
      "notes": "Very handsome customer",
      "person_id": 1,
      "resource_id": 1,
      "service_id": 1,
      "state": "confirmed",
      "status": null,
      "updated_at": "2012-09-20T15:34:16+02:00"
    }
  }
]
```

### Query Parameters

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>service_id</td><td>Integer or array of integers</td><td></td></tr>
  <tr><td>event_id</td><td>Integer or array of integers</td><td></td></tr>
  <tr><td>resource_id</td><td>Integer or array of integers</td><td></td></tr>
  <tr><td>person_id</td><td>Integer or array of integers</td><td></td></tr>
  <tr><td>external_id</td><td>String</td><td></td></tr>
  <tr><td>start</td><td>Datetime</td><td>booked_from after param</td></tr>
  <tr><td>end</td><td>Datetime</td><td>booked_to before param</td></tr>
  <tr><td>since</td><td>Datetime</td><td>updated_at after param</td></tr>
  <tr><td>collection_id</td><td>UUID</td><td></td></tr>
  <tr><td>state</td><td>String or array of strings</td><td>See states</td></tr>
  <tr><td>status</td><td>String or array of strings</td><td>See statuses</td></tr>
</table>

You can return bookings of multiple resources/services/events/people with an array.

### Other Parameters

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>extended</td><td>Boolean</td><td>Extend output with full data from related objects.</td></tr>
</table>

## Get booking

`GET /bookings/{booking_id}` will get booking with id `{booking_id}`.

You can also find a booking with `external_id`:

`GET /bookings/find_by/external_id/{external_id}` will get booking with external_id `{external_id}`.

## Add new booking

`POST /bookings` will create a new booking.

Bookings without `person_id` are shown as busy in the calendar.

### Non-returning booking parameters

These parameters are part of booking: `booking[public_booking]`.

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>public_booking</td><td>Boolean</td><td>Restrict to normal public booking rules such as within opening hours, not able to book in the past or past specified allowed date in the future. Recommended for all customer facing booking applications (e.g. integration on website). Not recommended when synchronising with other calendar programs. Default: false.</td></tr>
</table>

### Additional parameters

These parameters are not part of `booking[]`: `confirm`.

Outgoing messages requires person_id and service_id.

Default action for outgoing messages is according to the settings for booking verification. If email verification is enabled then all `*_email` messages will be triggered by default to the customer. To override please see parameters for each action below. For admin notification messages the default is based on the account settings. SMS reminders is based on account settings and uses a default value for the time unless specified.

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>confirm</td><td>Boolean</td><td>If set to false then the 'initiate verification' event is executed. If verification is required the state will be set to `awaiting_verification`. If no verification is required then the state will be set to `awaiting_confirmation` or `confirmed`.</td></tr>
  <tr><td>ignore_capacity</td><td>Boolean</td><td>Will force save the booking even though other bookings exists within the same time on the specified resource_id (double booking). Not applicable when `public_booking` is set to true (no double bookings allowed). Default: false.</td></tr>
  <tr><td>add_reminder_sms</td><td>Boolean</td><td>Automatically adds `reminder_at` based on setting from account attribute `sms_reminder_time`. Default: at 12PM the day before the appointment.</td></tr>
  <tr><td>add_messages</td><td>Boolean</td><td>Automatically generate messages based on message templates. (NOTE: Beta functionality available by invitation.)</td></tr>
  <tr><td>verification_send_email</td><td>Boolean</td><td>Send out verification email. Only applicable when `confirm` is set to false.</td></tr>
  <tr><td>verification_send_sms</td><td>Boolean</td><td>Send out verification SMS. Only applicable when `confirm` is set to false.</td></tr>
  <tr><td>confirmation_send_email</td><td>Boolean</td><td>Send out confirmation email.</td></tr>
  <tr><td>confirmation_send_sms</td><td>Boolean</td><td>Send out confirmation SMS.</td></tr>
  <tr><td>notification_send_email</td><td>Boolean</td><td>Send out notification email to admin.</td></tr>
  <tr><td>notification_send_sms</td><td>Boolean</td><td>Send out notification SMS to admin.</td></tr>
  <tr><td>extended</td><td>Boolean</td><td>Extend output with full data from related objects.</td></tr>
</table>

### Add new booking with new person

To add a new person along with a booking you must populate `person_attributes` with [person](/endpoints/people/) attributes. Values will be matched to an existing person based on national id number, phone number or email (in that order).

## Add recurring bookings

`POST /bookings/recurring` will create recurring bookings based on a pattern.

The recurrence format follows the [iCalendar specification](https://tools.ietf.org/html/rfc5545){:target="_blank"}. The attributes for recurrence are: `RRULE`, `RDATE`, `EXDATE`. For an introduction and examples of these parameters see [this section from the iCalendar specification](http://www.kanzaki.com/docs/ical/rrule.html){:target="_blank"}.

In the iCalendar specification the recurrence is based on values in `DTSTART` and `DTEND`. This is set by `booked_from` and `booked_to` from `booking`.

All recurring bookings will except for `booked_from` and `booked_to` have the same attributes based on the specified parameters in `booking`.

All bookings created by the recurring pattern gets the same UUID in `collection_id`. As the collection name implies, and as is possible with the iCalendar specification, this is not necessarily only for recurrence (i.e. 9am-10am each Friday until December 1st) but also for multiple specific times (as can be specified with `RDATE`).

Only the `collection_id` is returned. No bookings are created at the time of request as they will be processed by the server in the background due to the volume of bookings that it is possible to create at one time. A successful response with a `collection_id` does in no way indicate that any bookings will be created. The first booking as defined in `booking` is however validated. If it is not valid errors details are returned in the same way as creating a single booking. In such a case recurring rules are not applied and you must adjust the request until validation is successful.

### Parameters for recurrence

The parameters for recurrence are not set in `booking` but in `recurring`.

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>rrule</td><td>String</td><td>Repeating pattern. Example: `FREQ=DAILY;UNTIL=19971224T000000Z`.</td></tr>
  <tr><td>rdate</td><td>String</td><td>List of recurring dates. Example: `VALUE=DATE:19970101,19970120,19970217,19970421`.</td></tr>
  <tr><td>exdate</td><td>String</td><td>List of dates that should be excluded from the recurring rule. Example: `VALUE=DATE:19970102`.</td></tr>
</table>

You should always specify COUNT or UNTIL with RRULE. The max number of occurrences is 731, regardless if a limit is set or not.

### List occurrences

`GET /bookings/recurring/{collection_id}` will return all occurrences for a collection.

### Cancel all occurrences

`PUT /bookings/recurring/{collection_id}/cancel` will cancel *all* occurrences for a collection.

### Delete all occurrences

`DELETE /bookings/recurring/{collection_id}` will delete *all* occurrences for a collection.

## Change booking state

### Verify a booking

`PUT /bookings/{booking_id}/verify` will verify a booking. The verification code is not checked. Use this when you handle verification in your application.

### Verify a booking with verification code

`PUT /bookings/{booking_id}/verify_code` will verify a booking using `{verification_code}`. Use this when Makeplans is used to send out verification email or SMS.

#### Additional parameters

See [information about additional parameters](#additional-parameters).

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>confirmation_send_email</td><td>Boolean</td><td>Send out confirmation email.</td></tr>
  <tr><td>confirmation_send_sms</td><td>Boolean</td><td>Send out confirmation SMS.</td></tr>
  <tr><td>notification_send_email</td><td>Boolean</td><td>Send out notification email to admin. Default: based on account setting.</td></tr>
  <tr><td>notification_send_sms</td><td>Boolean</td><td>Send out notification SMS to admin.</td></tr>
  <tr><td>verification_code</td><td>String</td><td>Verification code send using SMS or email. Only applicable for the `verify_code` action.</td></tr>
</table>

### Confirm a booking

`PUT /bookings/{booking_id}/confirm` will confirm a booking.

#### Additional parameters

See [information about additional parameters](#additional-parameters).

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>confirmation_send_email</td><td>Boolean</td><td>Send out confirmation email.</td></tr>
  <tr><td>confirmation_send_sms</td><td>Boolean</td><td>Send out confirmation SMS.</td></tr>
  <tr><td>notification_send_email</td><td>Boolean</td><td>Send out notification email to admin.</td></tr>
  <tr><td>notification_send_sms</td><td>Boolean</td><td>Send out notification SMS to admin.</td></tr>
</table>

### Decline a booking

`PUT /bookings/{booking_id}/decline` will decline a booking.

#### Additional parameters

See [information about additional parameters](#additional-parameters).

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>decline_send_email</td><td>Boolean</td><td>Send out decline email.</td></tr>
  <tr><td>decline_send_sms</td><td>Boolean</td><td>Send out decline SMS.</td></tr>
</table>

### Cancel a booking

`PUT /bookings/{booking_id}/cancel` will cancel a booking.

#### Additional parameters

See [information about additional parameters](#additional-parameters).

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>cancellation_send_email</td><td>Boolean</td><td>Send out cancellation email.</td></tr>
  <tr><td>cancellation_send_sms</td><td>Boolean</td><td>Send out cancellation SMS.</td></tr>
</table>

## Update booking

`PUT /bookings/{booking_id}` will update a booking.

### Additional parameters

See [information about additional parameters](#additional-parameters).

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>ignore_capacity</td><td>Boolean</td><td>Set to true to allow a double booking.</td></tr>
  <tr><td>add_reminder_sms</td><td>Boolean</td><td>Automatically adds `reminder_at`.</td></tr>
  <tr><td>modification_send_email</td><td>Boolean</td><td>Send out modification email.</td></tr>
  <tr><td>modification_send_sms</td><td>Boolean</td><td>Send out modification SMS.</td></tr>
  <tr><td>extended</td><td>Boolean</td><td>Extend output with full data from related objects.</td></tr>
</table>

## Delete booking

`DELETE /bookings/{booking_id}` will delete existing booking with id `{booking_id}`.

Deleting a booking will set it to state=deleted and active=false. It will not be visible in listing, only when requesting `GET /bookings/all` or by requesting the booking directly `GET /bookings/{booking_id}`.

Do not use this method if the booking is rescheduled or cancelled.

### Additional parameters

See [information about additional parameters](#additional-parameters).

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>cancellation_send_email</td><td>Boolean</td><td>Send out cancellation email.</td></tr>
  <tr><td>cancellation_send_sms</td><td>Boolean</td><td>Send out cancellation SMS.</td></tr>
</table>

## Update booking status

`PUT /bookings/{booking_id}/status` will update the booking status.

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>status</td><td>String</td><td>Booking status.</td></tr>
</table>

This parameter is specified in the same way as [additional parameters](#additional-parameters).
