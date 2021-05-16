# Changelog

## 2021-05-16

**Added national_id_no and multiple column search for people**

Search can now be done on multiple columns by specyfing for example both email and phone number.

## 2021-03-23

**Added available dates endpoint**

List dates with available slots for a service.

## 2021-02-21

**Added wildcard triggers for webhooks**

You can now add wildcard entries when events are trigged. Example: `*` or `booking.*`.

## 2021-02-03

**Deprecate XML support**

*BREAKING CHANGE*

JSON is our only God now.

**Change client error response from 403 to 400**

*BREAKING CHANGE*

All errors based on client request input will not return a 400 Bad Request status code instead of 403 Forbidden.

**Lack of authorization now returns 403**

Currently you have full access with the API-key but if we support permission levels for API usage as we do with normal users you will receive a 403 status code when you are authenticated but lack permissions.

## 2021-01-18

**Added RFC-8288 pagination**

Paginated responses now uses RFC-8288 `Link` HTTP header. There is also metadata in HTTP headers: `Current-Page`, `Page-Items`, `Total-Count`, `Total-Pages`.

## 2020-11-23

**Removed booking agenda listing from documentation**

By mistake this was included in the documentation, it was never implemented.

Endpoints affected:
GET `/bookings/agenda/`

## 2020-09-09

**Added title to booking**

You can now set a title for a booking. In the GUI it is only displayed for bookings saved as occupied (without person_id).

## 2020-06-03

**Added HTTP Bearer token authentication**

The default authentication method is now using API-key as a token with Authorization Bearer header.

**Added signature for verifying webhook payload**

Webhooks now include a `X-MakePlans-Signature` header which you can use to verify the request body with.

## 2020-05-14

**Added delete all occurrences endpoint**

Added endpoint for deleting all occurrences of recurring bookings.

## 2020-03-13

**New webhook events**

Added the following webhooks events:

* booking.verified

**Fixed message sending bug**

*BREAKING CHANGE*

Fixed a parsing bug which caused certain boolean json values to be ignored when set to false. This applied to parameters for specifying message sending when creating a booking, such as `notification_send_email`. Using a string value of 'false' worked but not the boolean value false. This will be a breaking change if you sent these parameters as boolean false using JSON.

Endpoints affected:
POST `/bookings/*`
PUT `/bookings/*`
DELETE `/bookings/*`

## 2020-03-12

**Added phone_number to booking person**

`phone_number` is now included with person in a booking listing.


## 2019-12-11

**New webhook events**

Added the following webhook events:

* booking.declined
* booking.modified

## 2019-11-26

**Modified booking cancellation webhook event**

*BREAKING CHANGE*

Removing a booking would previously trigger `booking.cancelled` webhook event. This has now been changed to `booking.deleted`.

A cancellation would still trigger `booking.cancelled`. This means that to monitor deleted bookings you should subscribe to both `booking.cancelled` and `booking.deleted`. In most cases a booking will be cancelled, but in some cases it will be removed.

## 2019-03-21

**Added list users endpoint**

Added endpoint for listing users with access to client.

**Added add_messages to create booking**

*INVITE-ONLY BETA FUNCTIONALITY*

Can specify whether messages are generated from message templates.

## 2018-11-15

**Added delete person endpoint**

Added endpoint for deleting a person.

## 2017-12-30

**Added merge people endpoint**

Added endpoint for merging two people.

## 2017-12-28

**Default order of returned bookings and events**

The default order used to be reverse chronological, this have not changed to chronological. So the oldest entry will now be the first item returned.

To use the old default please specify order using `order=booked_from_desc` for bookings and `order=starts_at_desc` for events.

Endpoints affected:
GET `/bookings/`
GET `/events/` and other similar listing endpoints for bookings and events.


## 2017-08-10

**Maximum limit for recurring occurrences**

Previously there was a limit of 366 occurrences when creating recurring bookings or events. When going above this limit no detailed error message was returned. We now return a detailed error message on the `collection_id` attribute. In addition we have increased the maximum limit of occurrences to 731.

## 2017-07-05

**Verification method attribute requirement for existing person**

*BREAKING CHANGE*

When verification is required the attribute of the verification method is required to complete a booking with `confirm=false`. So if SMS verification is used then `phone_number` is a required field for `person_attributes`. This now also applies when specifying a `person_id`, so a person without a phone number will not be able to initiate the verification process.

In general it is advisable to only specify `person_id` when you have authenticated this person. In all other cases you should supply `person_attributes` so MakePlans can match and verify the person using the supplied values.

Endpoint affected: POST `/bookings/?confirm=false`

**Valid phone number required when verifying a booking**

*BREAKING CHANGE*

When SMS verification is used the supplied `phone_number` (either using `person_attributes` or the value from an existing person) is now validated to ensure it is a proper phone number. Currently any phone number can be specified but in the future only mobile phone numbers will be allowed since an SMS will be sent to the phone number.

Endpoint affected: POST `/bookings/?confirm=false`

**Public booking will not check for verification method attributes**

*BREAKING CHANGE*

Previously specifying `public_booking=true` would validate required verification method attribute (such as `phone_number`). From now on this validation is only performed when verification is requested by specifying `confirm=false`.

Endpoint affected: POST `/bookings/?public_booking=true`

**Removed `base` error for person errors when creating new booking**

*BREAKING CHANGE*

`base` is no longer returned when there is an error with the person supplied with a new booking. Instead the `person` attribute now contains information about errors related to the person. In addition detailed errors are returned in `person_attributes`.

Endpoint affected: POST `/bookings/`

Previously:
```json
{
  "base": [
    "Person errors"
  ]
}
```

Now:
```json
{
  "person": [
    "is invalid"
  ],
  "person_attributes": {
    "email": [
      "cannot be empty"
    ]
  }
}
```
