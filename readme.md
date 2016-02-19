# MakePlans API - Version 1.2

This is the API documentation for MakePlans. MakePlans is an online booking application with support for appointments, classes and events. For more information about the application and its features see http://makeplans.net.

MakePlans provides a fairly standard REST API. All features in MakePlans are available via the API.

General Information:
* [Get started](#get-started)
* [Base API URL](#base-api-url)
* [Versioning](#versioning)
* [Data formats](#data-formats)
* [Date handling](#date-handling)
* [Custom data](#custom-data)
* [Authentication](#authentication)
* [Identification](#identification)
* [Errors](#errors)
* [Pagination](#pagination)
* [Examples](#example-request-and-response)
* [Syncronisation](#syncronisation)
* [Client libraries](#client-libraries)
* [Web hooks](#web-hooks)

API Endpoints:
* [Slots](#slots)
* [Bookings](#bookings)
* [People](#people)
* [Services](#services)
* [Events](#events)
* [Resources](#resources)
* [Resource exception dates](#resource-exception-dates)
* [Providers](#providers)
* [Categories](#categories)
* [Users](#users)
* [Client](#client)

## Get started

1) Sign up for a trial account in our test-environment: http://app.test.makeplans.net/client/new

2) Enable the API and get the API-key from the account page.

3) Read this documentation.

4) Profit.

When your integration is ready to be released then you can sign up for a real account at https://app.makeplans.net/client/new

## Base API URL

The base URL is `https://youraccount.makeplans.net/api/` for production apps and `http://youraccount.test.makeplans.net/api/` for test apps. All requests in the production environment are done over HTTPS. Please note that the staging environment is not encrypted (HTTP only). Each object in MakePlans has its own endpoints.

## Versioning

The current version of the API is version 1. The versioning scheme is as follows: `/api/v{version_number}/`. All paths in the rest of the document uses `/api/v1/` as base path.

Please keep up to date with the API as older versions may be deprecated.

## Data formats

The API supports JSON and XML for input and output. In addition you can specify input using form data.

The format of the output data is provided as is and the reference is this document. We do not use XML Schema. Any additional attributes such as 'type' in XML must be considered only as hints.

All data is UTF-8.

All examples and object attributes in this documentation are JSON. They are lowercase and use underscore as seperator. XML element names uses hypen instead of underscore (`created-at` instead of `created_at`).

### Output

To specify JSON as output use HTTP-header `Accept: application/json`. Output can also be defined by using extension in the path: `/resources.json` but it is recommended to use `/resources` and specify output format in the HTTP-header. Multiple items is returned as an array.

### Input

To specify JSON as input use HTTP-header `Content-Type: application/json`. The body must be JSON-formatted and include the object with required attributes.

To use normal form data specify HTTP-header `application/x-www-form-urlencoded`. Form values should be sent like this: `resource[title]=Unicorn`.

## Date handling

All dates are specified in the ISO 8601 format. Timezone is included in the output and specified by the account. It is not necessary to specify timezone in the input as the account timezone will be used as default. The output will give a full ISO 8601 date format with time zone: `YYYY-MM-DDThh:mm:ssTZD`. For input we recommend that you do not specify time zone unless needed and ommit seconds: `YYYY-MM-DD hh:mm`.

## Custom data

Custom data is stored as key/value. All values are stored as strings but we do convert boolean values and values from keys ending in '_at' to datetime at output. Custom data can be added to booking, person, service, resource and event. If you are using our standard booking site and would like to store custom data to a new booking please see https://github.com/makeplans/makeplans-custom-forms for details on how to customise the booking form.

Custom data is stored as one attribute. **All** keys and values must be present when updating. If a booking has stored `custom_data` as 'makeplans_is=awesome' and you want to add 'signup=now' then you need to include both 'makeplans_is' and 'signup' with their values.

## Authentication

MakePlans uses HTTP Basic Auth. The client has to enable the API first and you will find the API-Key in the account settings. The API-Key is the username and there is no password. MakePlans uses SSL and all requests over http will be redirected to https.

If your app is installable by end-users you should use oAuth. However we do not yet support oAuth so please contact us if this is something you require.

## Identification

You must include a User-Agent HTTP-header with the name of your application and a link to it or your email address so we can get in touch in case you're doing something wrong (so we may warn you before you're blacklisted) or something awesome (so we may congratulate you). Example: `User-Agent: YourAppName (http://example.com)`.

## Errors

4xx HTTP status codes means you made a mistake and you need to adjust your request.

### 401 - Unauthorized

Authentication error. Response body will give explanation if there is authorisation issue or if the API is not enabled.

### 402 - Payment Required

*Not yet implemented*

Please pay your bill.

### 403 - Forbidden

API usage error. This means you did something wrong and there should be a message in the body that explains it. Error message is related to specified resource. Fix it and try again.

Example response:

```json
{
  "title": [
  "is a required field. Cannot be empty"
  ]
}
```

### 404 - Not Found

Obviously incorrect paths (`/cats`) returns 404. However, even though cool URIs should not change, previously available objects, lets say `/resources/666`, might have been deleted and thus return a 404 when requested. In most cases deleted resources will be returned and have a booking state or a flag that indicate that the resource is inactive or deleted.

### 429 - Too Many Requests

You can perform up to 120 requests per 60 second period from the same IP address. If you exceed this limit, you'll get a 429 Too Many Requests response for subsequent requests. Check the `Retry-After` HTTP-header to see how many seconds to wait before retrying the request.

### 5xx - Server error

System errors (aka we screwed up) returns 5xx HTTP status codes without any detailed information. We log all system errors, but please contact us if you get this response.

```json
{
  "error": "system error"
}
```

## Pagination

Maximum 100 results are returned per page. Specify page with parameter `page`. Pagination is used for: bookings, events, people and resource exception dates. All other objects return all available items. Please note that currently there is no way to specify exact part of the dataset. Thus an item might appear in both page 2 and 3 if the complete dataset for the query has changed.

## Example request and response

```shell
curl -u APIKEY: \
-H 'User-Agent: YourAppName (http://example.org)' \
-H 'Accept: application/json' \
https://youraccount.makeplans.net/api/v1/services
```

To create something you have to make a POST:

```shell
curl -u APIKEY: \
-H 'User-Agent: YourAppName (http://example.org)' \
-H 'Accept: application/json' \
-d 'service[title]=My new service!&service[interval]=40' \
-X POST \
https://youraccount.makeplans.net/api/v1/services
```

## Syncronisation

Syncronising data is hard. Please ensure you test before releasing to production. First pick either MakePlans or the other system as a master. If the other system is chosen as a master then we recommend enabling the 'confirmation by administrator' setting for bookings. The synchronisation should then retrieve unprocessed bookings and process them (confirm/decline). This will ensure you can handle any changes occured in the other system since the last syncronisation with MakePlans. New unprocessed bookings must be processed often (every 1-5 minutes) to ensure confirmations are sent out quickly to the end-user after requesting a new reservation.

**NEVER** delete any data in MakePlans to make it easier to adapt to the other system. MakePlans is a customer facing booking application. End-users (stored as people) can change and cancel bookings, thus any modifications or destruction of core data should not occur. Bookings are stored with a history (log) and there is a link between a person and bookings. MakePlans make use of this data and all this data must be kept to ensure the booking process in MakePlans works as expected for the end-user.

Expect all booking and person data to be changed at any time. All changes for an object will result in a updated attribute `updated_at`.

We recommened storing a timestamp for when the syncronisation was last performed. When the syncronisation is performed again this timestamp can be used to fetch any changes on the `updated_at` attribute for the object you want to retrieve (e.g. the parameter `since` for bookings).

## Client libraries

MakePlans does not officially support client libraries but they might be useful for you. Please note that these projects are **not** made by MakePlans but made publically available by other developers who have used the MakePlans API. Any questions should be made directly to the responsible developers. If you find any errors or areas of improvement please make a pull request to improve the project.

* NodeJS: https://github.com/mableteam/makeplans
* CakePHP: https://github.com/Pollenizer/CakePHP-MakePlans-Plugin
* Go: https://github.com/drewwells/makeplans

## Web hooks

*Not yet implemented*

A webhook is simply a user-defined callback in the form of an HTTP POST which is invoked when something happens.
So whenever a new booking is created in MakePlans we can send a POST request to the URL you specify. The response will include data about the modified object.

## Slots

Slots are not physical objects in MakePlans. It is a virtual representation of available times based on attributes from resources and services. So if a resource is open 8am to 16pm and selected service has interval of 60 minutes, slots will return an array of all time intervals (8am-9am, 9am-10am etc.) and indicate which recoures are available.

Slots are meant for listing available times on the MakePlans booking page. You can however make bookings at any time and with any length - as long as the resource is available off course.

### Attributes

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>timestamp</td><td>DateTime</td><td>Start</td></tr>
  <tr><td>timestamp_end</td><td>DateTime</td><td>End</td></tr>
  <tr><td>formatted_timestamp</td><td>String</td><td>Localised human readable format</td></tr>
  <tr><td>formattet_timestamp_end</td><td>String</td><td>Localised human readable format</td></tr>
  <tr><td>free</td><td>Integer</td><td>Free capacity</td></tr>
  <tr><td>open_resources</td><td>Array</td><td>Ids of open resources</td></tr>
  <tr><td>available_resources</td><td>Array</td><td>Ids of resources with availability</td></tr>
</table>

### Listing

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
      "open_resources": [
      1
      ],
      "available_resources": [
      1,2
      ]
    }
  }
]
```

#### Query Parameters

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>from</td><td>Date</td><td>Default: today</td></tr>
  <tr><td>to</td><td>Date</td><td>Default: today</td></tr>
  <tr><td>selected_resources</td><td>Array</td><td>Default: all active providers.</td></tr>
</table>

### Next available date

`GET /services/{service_id}/next_available_date` will return the next available date within 30 days with a free slot.

Response

```json
[
  {
    "available_date": "2016-02-20"
  }
]
```

#### Query Parameters

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>from</td><td>Date</td><td>Default: today.</td></tr>
  <tr><td>selected_resources</td><td>Array</td><td>Default: all active providers.</td></tr>
</table>

## Bookings

### Attributes

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>id</td><td>Integer</td><td>Automatically set</td></tr>
  <tr><td>created_by</td><td>DateTime</td><td>Automatically set</td></tr>
  <tr><td>updated_by</td><td>DateTime</td><td>Automatically set</td></tr>
  <tr><td>service_id</td><td>Integer</td><td>Not required</td></tr>
  <tr><td>event_id</td><td>Integer</td><td>Not required</td></tr>
  <tr><td>resource_id</td><td>Integer</td><td>Required</td></tr>
  <tr><td>person_id</td><td>Integer</td><td>Not required</td></tr>
  <tr><td>booked_from</td><td>Datetime</td><td>Required</td></tr>
  <tr><td>booked_to</td><td>Datetime</td><td>Required</td></tr>
  <tr><td>expires_at</td><td>Datetime</td><td>Not required</td></tr>
  <tr><td>notes</td><td>Text</td><td>Not required</td></tr>
  <tr><td>reminder_at</td><td>DateTime</td><td>Not required</td></tr>
  <tr><td>reminded_at</td><td>DateTime</td><td>Not required</td></tr>
  <tr><td>paid_at</td><td>DateTime</td><td>Not required</td></tr>
  <tr><td>external_id</td><td>String</td><td>Not required</td></tr>
  <tr><td>google_order_number</td><td>String</td><td>Not required</td></tr>
  <tr><td>custom_data</td><td>Array</td><td>Not required.</td></tr>
  <tr><td>state</td><td>String</td><td>Automatically set</td></tr>
  <tr><td>active</td><td>Boolean</td><td>Automatically set</td></tr>
  <tr><td>person_attributes</td><td>Object</td><td>See person</td></tr>
  <tr><td>count</td><td>Integer</td><td>Default: 1</td></tr>
  <tr><td>collection_id</td><td>UUID</td><td>Automatically set for recurring bookings.</td></tr>
</table>

### States

* awaiting_verification
* verification_expired
* awaiting_payment
* awaiting_confirmation
* confirmed
* declined
* cancelled
* deleted

#### Normal state flow

The normal booking flow when a customer initiates a new booking starts with `awaiting_verification`. If verification is required MakePlans sends out email or SMS for the customer to verify. When successfully verified the state is updated to `confirmed` or `awaiting_confirmation` if reservations requires confirmation by administrator. For confirmation it is then updated to `confirmed` as normally if confirmed, and to `declined` if it is not confirmed.

Bookings that have been confirmed and then cancelled, either by customer or administrator, are set to `cancelled`.

Bookings that are deleted are set to `deleted`.

### Active bookings

Bookings with states `awaiting_verification`, `awaiting_confirmation` or `confirmed` are considered to be active. Bookings with state `awaiting_verification` will be updated with state `verification_expired` after the current time passes `expires_at`. However updating states rely on automatic tasks so you must use the `active` attribute to check whether a booking is active or not. Only active bookings will be returned unless you specify: a booking by id, a specific state such as bookings that are awaiting confirmation, to return all bookings for a resource or dates, or for a person.

### Listing

`GET /bookings` will return all active bookings. See query parameters for options.

`GET /bookings/recent` will return all active bookings updated today.

`GET /bookings/upcoming` will return all active bookings from and including `{date}`.

`GET /bookings/agenda` will return all active bookings within `{date}`.

`GET /bookings/unconfirmed` will return all unconfirmed bookings.

`GET /bookings/all` will return all bookings of all states (including `declined`, `cancelled`, `expired` and `deleted`).

Response

```json
[
  {
    "booking": {
      "booked_from": "2012-09-29T07:00:00+02:00",
      "booked_to": "2012-09-29T08:00:00+02:00",
      "created_at": "2012-09-20T15:34:16+02:00",
      "custom_data": {},
      "count": 1,
      "expires_at": null,
      "external_id": null,
      "id": 1,
      "notes": "Very handsome client",
      "person_id": 1,
      "resource_id": 1,
      "service_id": 1,
      "state": "confirmed",
      "updated_at": "2012-09-20T15:34:16+02:00"
    }
  }
]
```

#### Query Parameters

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>service_id</td><td>Integer</td><td></td></tr>
  <tr><td>event_id</td><td>Integer</td><td></td></tr>
  <tr><td>resource_id</td><td>Integer</td><td></td></tr>
  <tr><td>person_id</td><td>Integer</td><td></td></tr>
  <tr><td>external_id</td><td>String</td><td></td></tr>
  <tr><td>start</td><td>DateTime</td><td>booked_from after param</td></tr>
  <tr><td>end</td><td>DateTime</td><td>booked_to before param</td></tr>
  <tr><td>since</td><td>DateTime</td><td>updated_at after param</td></tr>
  <tr><td>collection_id</td><td>UUID</td><td></td></tr>
</table>

### Add new booking

`POST /bookings` will create a new booking.

#### Non-returning booking parameters

These parameters are part of booking eq.: `booking[public_booking]`.

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>public_booking</td><td>Boolean</td><td>Restrict to normal public booking rules such as within opening hours, not able to book in the past or past specified allowed date in the future. Recommended for all customer facing booking applications (e.g. integration on website). Not recommended when syncronising with other calendar programs. Default: false.</td></tr>
</table>

#### Additional parameters

These parameters are part not of `booking` eq.: `confirm`. When saving a new booking all of the next three sections applies.

Outgoing messages requires person_id and service_id.

##### When creating a new booking

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>confirm</td><td>Boolean</td><td>If set to false then the 'initiate verification' event is executed. If verification is required the state will be set to `awaiting_verification`. If no verification is required then the state will be set to `awaiting_confirmation` or `confirmed`.</td></tr>
  <tr><td>verification_send_email</td><td>Boolean</td><td>Send out verification email. Only applicable when `confirm` is set to false. Default: false.</td></tr>
  <tr><td>verification_send_sms</td><td>Boolean</td><td>Send out verification SMS. Only applicable when `confirm` is set to false. Default: false.</td></tr>
</table>

##### When creating a new or updating an existing booking

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>add_reminder_sms</td><td>Boolean</td><td>Automatically adds `reminder_at` based on setting from client atribute `sms_reminder_time`. Default: at 12PM the day before the appointment.</td></tr>
  <tr><td>ignore_capacity</td><td>Boolean</td><td>Will force save the booking even though other bookings exists within the same time on the specified resource_id. Default: false.</td></tr>
</table>

##### When confirming a booking

Either saving a new booking and confirming it, or using the events `confirm` or `verify` (might not always result in a confirmed booking though - can be set to `awaiting_confirmation` based on client settings).

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>confirmation_send_email</td><td>Boolean</td><td>Send out confirmation email. Default: false.</td></tr>
  <tr><td>confirmation_send_sms</td><td>Boolean</td><td>Send out confirmation SMS. Default: false.</td></tr>
  <tr><td>notification_send_email</td><td>Boolean</td><td>Send out notification email to admin. Default: based on client setting.</td></tr>
  <tr><td>notification_send_sms</td><td>Boolean</td><td>Send out notification SMS to admin. Default: based on client setting.</td></tr>
</table>

#### Add new booking with new person

To add a new person along with a booking you must use populate `person_attributes`. MakePlans will match to an existing person based on email or phone number (in that order).

### Add recurring/multiple bookings

`POST /bookings/recurring` will create a multiple bookings.

The recurrence format follows the [iCalendar specification](https://tools.ietf.org/html/rfc5545). The attributes for reccurence are: `RRULE`, `RDATE`, `EXDATE`. For an introduction and examples of these parameters see http://www.kanzaki.com/docs/ical/rrule.html.

In the iCalendar specification the recurrence is based on values in `DTSTART` and `DTEND`. This is set by `booked_from` and `booked_to` from `booking`.

All recurring bookings will except for `booked_from` and `booked_to` have the same attributes based on the specified parameters in `booking`.

All bookings created by the recurring pattern gets the same UUID in `collection_id`. As the collection name implies, and as is possible with the iCalendar specification, this is not necessarirly only for recurrence (i.e. 9am-10am each Friday until December 1st) but also for multiple specific times (as can be specified with `RDATE`).

Only the `collection_id` is returned. No bookings are created at the time of request as they will be processed by the server in the background due to the volume of bookings that it is possible to create at one time. A succesful response with a `collection_id` does in no way indicate that any bookings will be created. The first booking as defined in `booking` is however validated. If it is not valid errors details are returned in the same way as creating a single booking. In such a case recurring rules are not applied and you must adjust the request until validation is succesful.

#### Parameters for reccurrence

The parameters for reccurence are not set in `booking` but in `recurrence`.

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>rrule</td><td>String</td><td>Repeating pattern. Example: `FREQ=DAILY;UNTIL=19971224T000000Z`.</td></tr>
  <tr><td>rdate</td><td>String</td><td>List of recurring dates. Example: `VALUE=DATE:19970101,19970120,19970217,19970421`.</td></tr>
  <tr><td>exrule</td><td>String</td><td>Repeating exception pattern. Example: `FREQ=WEEKLY;COUNT=4;INTERVAL=2;BYDAY=TU,TH`.</td></tr>
  <tr><td>exdate</td><td>String</td><td>List of dates that should be excluded from the recurring rule. Example: `VALUE=DATE:19970102`.</td></tr>
</table>

You should always specify COUNT or UNTIL with RRULE. The max number of occurrences is 366, regardless if a limit is set or not.

#### List occurences

`GET /bookings/recurring/{collection_id}` will return all occurrences for a collection.

### Change booking state

#### Verify a booking

`PUT /bookings/{booking_id}/verify` will verify a booking.

#### Confirm a booking

`PUT /bookings/{booking_id}/confirm` will confirm a booking.

#### Decline a booking

`PUT /bookings/{booking_id}/decline` will decline a booking.

#### Cancel a booking

`PUT /bookings/{booking_id}/cancel` will cancel a booking.

### Update booking

`PUT /bookings/{booking_id}` will update a booking.

### Delete booking

`DELETE /bookings/{booking_id}` will delete existing booking with id `{booking_id}`.

Deleting a booking will set it to state=deleted and active=false. It will not be visible in listing, only when requesting `GET /bookings/all` or by requesting the booking directly `GET /bookings/{booking_id}`.

Do not use this method if the booking is rescheduled or cancelled.

## People

The primary key for a person is `id`. However the following fields are unique: `email`, `phonenumber` and `provider`+`uid`. There are no specific requirements for input but a person needs to have either name, email or phone number.

`national_id_no` is currently not unique or used as identificator for a person. This will change.

### Attributes

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>id</td><td>Integer</td><td>Automatically set</td></tr>
  <tr><td>created_at</td><td>Datetime</td><td>Automatically set</td></tr>
  <tr><td>updated_at</td><td>Datetime</td><td>Automatically set</td></tr>
  <tr><td>name</td><td>String</td><td>Not required.</td></tr>
  <tr><td>email</td><td>String</td><td>Not required.</td></tr>
  <tr><td>phone_number</td><td>String</td><td>Not required. Phone number as stored. Also available as phonenumber (deprecated).</td></tr>
  <tr><td>phone_number_formatted</td><td>String</td><td>Only returnable. E.164 formatted phone number with plus sign, international code, and no spaces or parentheses.</td></tr>
  <tr><td>external_id</td><td>String</td><td>Not required</td></tr>
  <tr><td>custom_data</td><td>Array</td><td>Not required. Key/value. Stored as strings.</td></tr>
  <tr><td>date_of_birth</td><td>Date</td><td>Not required</td></tr>
  <tr><td>national_id_no</td><td>String</td><td>Not required.</td></tr>
  <tr><td>street</td><td>String</td><td>Not required.</td></tr>
  <tr><td>city</td><td>String</td><td>Not required.</td></tr>
  <tr><td>postal_code</td><td>String</td><td>Not required.</td></tr>
  <tr><td>state</td><td>String</td><td>Not required.</td></tr>
  <tr><td>country_code</td><td>String</td><td>Not required. ISO 3166-1 alpha-2.</td></tr>
  <tr><td>notes</td><td>Text</td><td>Not required.</td></tr>
</table>

### Listing

Searching unique fields (`email`, `phonenumber`, `external_id`, `uid`+`provider`) will return only one result if found.

Response

```json
[
  {
    "person": {
      "created_at": "2012-09-20T15:34:16+02:00",
      "custom_data": {},
      "date_of_birth": null,
      "email": "espen@makeplans.no",
      "external_id": null,
      "id": 1,
      "name": "Espen Antonsen",
      "national_id_no": null,
      "notes": null,
      "phonenumber": "",
      "updated_at": "2012-09-20T15:34:16+02:00",
      "phone_number_formatted": null
    }
  }
]
```

#### Query Parameters

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>email</td><td>String</td><td></td></tr>
  <tr><td>phonenumber</td><td>String</td><td></td></tr>
  <tr><td>external_id</td><td>String</td><td></td></tr>
  <tr><td>name</td><td>String</td><td></td></tr>
  <tr><td>date_of_birth</td><td>Date</td><td></td></tr>
  <tr><td>national_id_no</td><td>String</td><td></td></tr>
</table>

### Add new person

`POST /people` will create a new person.

### Update person

`PUT /people/{person_id}` will update existing person with id `{person_id}`.

### Delete person

*Not yet implemented*

### Person verification with a perishable token

The purpose of this feature is to verify a new or existing person in MakePlans on your website such as "login to see your bookings". The verification can be done either using email or SMS. When successful you will have verified either an email or a phone number. Thus you can either create a new person or retrieve an existing person knowing that the person has been verified.

Please note that this token is perishable, meaning that it will be removed from and unavailable for verification after 10 minutes.

#### Send perishable token

`POST /people/perishable_token/send` will send a SMS with a five digit code (the token) or an email with a link which includes the token in the URL. Specify either `person[email]` or `person[phone_number]`.

#### Verify perishable token

`POST /people/perishable_token/verify`. This will return a person if authentication is successful. If the email or phone_number is already registered to a person then that person will be returned. If no person exists with the specified email/phone_number then a empty person object will be returned. You then have to make another call to create the person. If the token and the identifier do not match an error is returned.

Specify the token as `perishable_token` and the identifier in `person[email]` or `person[phone_number]`.

### Authenticate existing person with a password

Authentication of person is done using a unique field: `{email}` or `{phone_number}` and `{password}`.

`POST /people/authenticate`. This will return a person if authentication is successful. You are still using your API-key so while the method is called `authenticate` it is just a way to verify the credentials of a person.

### List bookings for person

`GET /people/{person_id}/bookings` will retrieve all bookings for person with id `{person_id}`.

## Services

There are two types of services:

* Appointment.
* Attendance.

Appointments can be booked within fixed opening hours as specified on the Resource and with exceptions specified in ResourceExceptionDate.

Attendance at an event is also a booking but the individual booking datetime or resource cannot be modified. Attendance is linked to an event which occurs at a specific time. One service can be linked to one or multiple events.

### Attributes

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>id</td><td>Integer</td><td>Automatically set</td></tr>
  <tr><td>created_at</td><td>Datetime</td><td>Automatically set</td></tr>
  <tr><td>updated_at</td><td>Datetime</td><td>Automatically set</td></tr>
  <tr><td>title</td><td>String</td><td>Required</td></tr>
  <tr><td>active</td><td>Boolean</td><td>Automatically set</td></tr>
  <tr><td>booking_capacity</td><td>Integer</td><td>Not required</td></tr>
  <tr><td>day_booking_specify_time</td><td>Boolean</td><td>Not Required (default false)</td></tr>
  <tr><td>has_day_booking</td><td>Boolean</td><td>Not required (default false)</td></tr>
  <tr><td>interval</td><td>Integer</td><td>Not required (default 60)</td></tr>
  <tr><td>max_slots</td><td>Integer</td><td>Not required (default 1)</td></tr>
  <tr><td>price</td><td>Decimal</td><td>Not required. Uses currency on client.</td></tr>
  <tr><td>same_day</td><td>Boolean</td><td>Not required (default false)</td></tr>
  <tr><td>template</td><td>String</td><td>Component template (calendar view)</td></tr>
  <tr><td>interval_rounding</td><td>Integer</td><td>Overrides client default (see info on client object)</td></tr>
  <tr><td>booking_type_id</td><td>Integer</td><td>1: appointment. 2: attendance.
    <tr><td>custom_data</td><td>Array</td><td>Not required.</td></tr>
  </td></tr>
</table>

### Listing

Response

```json
[
  {
    "service": {
      "active": true,
      "booking_capacity": 1,
      "created_at": "2012-09-20T15:34:16+02:00",
      "custom_data": {},
      "day_booking_specify_time": false,
      "has_day_booking": false,
      "id": 1,
      "interval": 20,
      "max_slots": null,
      "price": "115.0",
      "same_day": false,
      "template": null,
      "title": "Chiropractor",
      "updated_at": "2012-09-20T15:34:16+02:00"
    }
  }
]
```

### Add new service

`POST /services` will create a new service.

### Update service

`PUT /services/{service_id}` will update existing service with id `{service_id}`.

### Delete service

`DELETE /services/{service_id}` will delete existing service with id `{service_id}`. Deleting a service will set it to active=false and will not be returned in any listings.

## Events

Unlike appointments made through a normal service events starts and ends at a specific time. An event is connected to a resource and a service. It could be either a one-off event (e.q. a concert) or something that occurs multiple times (e.q. spinning class). Event bookings have a strong relation to the event. That means that it is not possible to modify details such as `{booked_from}`, `{booked_to}`, `{resource_id}` and `{service_id}` for the `{booking}`. To make such changes it must be done to the event. All bookings connected to the event will then automatically me modified.

While events are connected to a resource bookings or capacity of an event are not restricted by the opening hours or availability of a resource.

### Attributes

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
  <tr><td>description</td><td>Text</td><td></td></tr>
</table>

### Listing

Response

```json
[
  {
    "event": {
      "capacity": 10,
      "created_at": "2012-09-20T15:34:16+02:00",
      "custom_data": {},
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

#### Query Parameters

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>service_id</td><td>Integer</td><td></td></tr>
  <tr><td>resource_id</td><td>Integer</td><td></td></tr>
  <tr><td>start</td><td>DateTime</td><td>starts_at after param</td></tr>
  <tr><td>end</td><td>DateTime</td><td>ends_at before param</td></tr>
  <tr><td>since</td><td>DateTime</td><td>updated_at after param</td></tr>
</table>

### Add new event

`POST /events` will create a new event.

### Update event

`PUT /events/{event_id}` will update existing service with id `{event_id}`.

### Delete event

`DELETE /events/{event_id}` will delete existing event with id `{event_id}`. Deleting a event will set it to active=false and will not be returned in any listings.

## Resources

### Attributes

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

#### Default opening hours

Values for the opening hours attributes is of type time in the array. Values are in the form of two's. This results in adding breaks within a day. To define opening hours from 8AM to 4PM with lunch at 12PM to 12.30PM the following array will be the result: `['08:00', '12:00', '12:30', '16:00']`. Having opening hours without a lunch break will yield this result: `['08:00', '16:00']`. To define a weekday as closed the value should be `NULL`.

#### Deprecated Attributes

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

### Listing

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

### Get resource

`GET /resources/{resource_id}` will get a resource with id `{resource_id}`.

### Get opening hours for specific dates

Opening hours for specific dates is based on the standard opening hours that are specified on the resource but can be overridden with entries in [resource exception dates](#resource-exception-dates).

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


#### Query Parameters

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>from</td><td>Date</td><td></td></tr>
  <tr><td>to</td><td>Date</td><td></td></tr>
</table>

`GET /resources/{resource_id}/opening_hours` will get opening hours on specific dates for resource with id `{resource_id}`.

### Add new resource

`POST /resources` will create a new resource.

### Update resource

`PUT /resources/{resource_id}` will update existing resource with id `{resource_id}`.

### Delete resource

`DELETE /resources/{resource_id}` will delete existing resource with id `{resource_id}`. Deleting a resource will set it to active=false and will not returned in any listings.

## Resource exception dates

Opening hours for a resource is what is used to generate available slots for a service. It is based on the default opening hours on the resource unless anything else is specified by a resource exception date. So ignore the poorly naming of the feature and treat it as opening hours on dates. If a resource should be closed for a week then you would add resource exception dates. If a resource should have opening hours 08:00 to 14:00 on January 5th 2016 instead of the default opening hours for that weekday which could be 09:00 to 15:00, then add it as a resource exception date. See [resource opening hours](#default-opening-hours) for how to specify.

Exception dates are related to a resource: `GET /resources/{resource_id}/exception_dates`.

### Attributes

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>created_at</td><td>Datetime</td><td>Automatically set</td></tr>
  <tr><td>updated_at</td><td>Datetime</td><td>Automatically set</td></tr>
  <tr><td>resource_id</td><td>Integer</td><td>Required (specified in URL)</td></tr>
  <tr><td>exception_date</td><td>Date</td><td>Required (specified in URL)</td></tr>
  <tr><td>opening_hours</td><td>Array</td><td>Same format as resource opening hours</td></tr>
</table>

### Listing

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

#### Query Parameters

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>from</td><td>Date</td><td></td></tr>
  <tr><td>to</td><td>Date</td><td></td></tr>
</table>

### Get exception date

`GET /resources/{resource_id}/exception_dates/{exception_date}` will get the exception date `{exception_date}` for resource `{resource_id}`. If no existing entry is found a 404 HTTP status code is returned. In that case standard opening hours apply.

### Add new or update exception date

`POST /resources/{resource_id}/exception_dates/{exception_date}` will create a new or update an existing exception date `{exception_date}` for resource `{resource_id}`. You can also use `PUT`, both methods works the same way regardless if it is a new entry or an update to an existing one.

### Add new or update multiple exception dates

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


### Delete exception date

`DELETE /resources/{resource_id}/exception_dates/{exception_date}` will delete existing exception date `{exception_date}` for resource `{resource_id}`.

## Providers

Resources provides services. This link is called a provider.

### Attributes

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>id</td><td>Integer</td><td>Automatically set</td></tr>
  <tr><td>created_at</td><td>Datetime</td><td>Automatically set</td></tr>
  <tr><td>updated_at</td><td>Datetime</td><td>Automatically set</td></tr>
  <tr><td>active</td><td>Boolean</td><td>Automatically set</td></tr>
  <tr><td>resource_id</td><td>Integer</td><td>Required</td></tr>
  <tr><td>service_id</td><td>Integer</td><td>Required</td></tr>
</table>

### Listing

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

### Add new provider

`POST /providers` will create a new provider.

### Delete provider

`DELETE /providers/{provider_id}` will delete existing provider with id `{provider_id}`.

## Categories

Services can be listed in a category. Categories can be presented like a tree. Root categories are defined with empty `{parent_id}`.

### Attributes

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>id</td><td>Integer</td><td>Automatically set</td></tr>
  <tr><td>created_at</td><td>Datetime</td><td>Automatically set</td></tr>
  <tr><td>updated_at</td><td>Datetime</td><td>Automatically set</td></tr>
  <tr><td>title</td><td>String</td><td>Required</td></tr>
  <tr><td>parent_id</td><td>Integer</td><td></td></tr>
  <tr><td>custom_data</td><td>Array</td><td>Not required.</td></tr>
</table>

### Listing

Response

```json
[
  {
    "category": {
      "created_at": "2012-09-20T15:34:16+02:00",
      "custom_data": {},
      "id": 1,
      "parent_id": null,
      "title": "My first little category",
      "updated_at": "2012-09-20T15:34:16+02:00"
    }
  }
]
```

### Add new category

`POST /categories` will create a new category. To add connected services specify array of `{service_id}` in parameter `{service_id_list}`.

### Update category

`PUT /categories/{category_id}` will update existing category with id `{category_id}`. To add connected services specify array of `{service_id}` in parameter `{service_id_list}`.

### Delete category

`DELETE /categories/{category_id}` will delete existing category with id `{category_id}`.

## Users

Users who can login into the account.

### Attributes

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>id</td><td>Integer</td><td>Automatically set</td></tr>
  <tr><td>created_at</td><td>Datetime</td><td>Automatically set</td></tr>
  <tr><td>updated_at</td><td>Datetime</td><td>Automatically set</td></tr>
  <tr><td>name</td><td>String</td><td>Required</td></tr>
  <tr><td>email</td><td>String</td><td>Required</td></tr>
  <tr><td>phonenumber</td><td>String</td><td></td></tr>
</table>

## Client

Information and settings for your account.

### Attributes

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>id</td><td>Integer</td><td>Automatically set</td></tr>
  <tr><td>created_at</td><td>Datetime</td><td>Automatically set</td></tr>
  <tr><td>updated_at</td><td>Datetime</td><td>Automatically set</td></tr>
  <tr><td>subdomain</td><td>String</td><td>Required</td></tr>
  <tr><td>email</td><td>String</td><td>Required</td></tr>
  <tr><td>name</td><td>String</td><td>Required</td></tr>
  <tr><td>mail_verification</td><td>Text</td><td></td></tr>
  <tr><td>mail_confirmation</td><td>Text</td><td></td></tr>
  <tr><td>google_analytics</td><td>String</td><td></td></tr>
  <tr><td>css_url</td><td>String</td><td></td></tr>
  <tr><td>time_zone</td><td>String</td><td>Required</td></tr>
  <tr><td>verification_method</td><td>Integer</td><td></td></tr>
  <tr><td>new_user_text</td><td>Text</td><td></td></tr>
  <tr><td>address</td><td>String</td><td></td></tr>
  <tr><td>country_code</td><td>String</td><td>Required. ISO 3166-1 alpha-2.</td></tr>
  <tr><td>google_merchant_id</td><td>String</td><td></td></tr>
  <tr><td>google_merchant_key</td><td>String</td><td></td></tr>
  <tr><td>booking_form</td><td>Text</td><td></td></tr>
  <tr><td>logo</td><td>File</td><td></td></tr>
  <tr><td>template</td><td>Integer</td><td>Required</td></tr>
  <tr><td>locale</td><td>String</td><td>Required. Supported locales: 'en' and 'no-NB'. Default 'en'.</td></tr>
  <tr><td>email_notification</td><td>Boolean</td><td></td></tr>
  <tr><td>mail_notification_recipient</td><td>Email</td><td></td></tr>
  <tr><td>sms_notification</td><td>Boolean</td><td></td></tr>
  <tr><td>phone_number</td><td>String</td><td></td></tr>
  <tr><td>confirm_manually</td><td>Boolean</td><td></td></tr>
  <tr><td>settings</td><td>Array</td><td></td></tr>
  <tr><td>interval_rounding</td><td>Integer</td><td>Default setting for all services for rounding to next available time. '15' will round 10:07 to become 10:15 as next available slot time.</td></tr>
  <tr><td>slot_generation_type</td><td>Integer</td><td>Default: 2. 1=fixed. 2=next available.</td></tr>
</table>

#### Additional paramaters

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>remove_logo</td><td>Boolean</td><td></td></tr>
</table>

### Update client

*Currently not recommended as we have not yet documented all the attributes. Please contact us for clarification*

`PUT /client` will update the account.

### Holidays

Who doesn't like a holiday?

Response

```json
[
  {
    "holiday": {
      "date": "2016-01-01",
      "name": "New Year's Day"
    }
  },
  {
    "holiday": {
      "date": "2016-05-01",
      "name": "International Workers' Day"
    }
  }
]
```


#### Query Parameters

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>from</td><td>Date</td><td></td></tr>
  <tr><td>to</td><td>Date</td><td></td></tr>
</table>

`GET /client/holidays` will get holidays for client country.