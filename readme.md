# MakePlans API - Version 1.0

MakePlans provides a fairly standard REST API. The base URL is `https://youraccount.makeplans.no/api/`. All requests are done over HTTPS.

## Versions

The current version of the API is version 1. The versioning scheme is as follows: `/api/v1/` The most recent version of the API is always at `/api/`. So all calls to `/api/resources` are redirected to the current version, at the time being: `/api/v1/resources`.

Please keep up to date with the API as older versions may be deprecated.

All paths in the rest of this document uses `/api/v1/` as base path.

## Data formats

The API supports JSON and XML for input and output.

We recommend JSON for output and normal form-data for input.

The format of the output data is provided as is and the referenece is this document. We do not use XML Schema. Any additional attributes such as 'type' in XML must be considered only as hints.

All data is UTF-8.

All examples and object attributes in this documentation are JSON. They are lowercase and use underscore as seperator. XML element names uses hypen instead of underscore (`created-at` instead of `created_at`).

### Output

To specify JSON as output use HTTP-header `Accept: application/json`. Output can also be defined by using extension in the path: `/resources.json` but it is recommended to use `/resources` and specify output format in the HTTP-header. Multiple items is returned as an array.

### Input

To specify JSON as input use HTTP-header `Content-Type: application/json`. The body must be JSON-formatted and include the object with required attributes. Alternatively you can use normal form data as input, in that case do not specify the Content-type HTTP-header. Form values are specified like this: `resource[title]=Unicorn`. Normal form input is preferred over JSON/XML to avoid parser issues.

All parameters that are not input for creating or updating objects should be sent as normal URL parameters. Example: `/bookings?page=2&resources_id=1`.

## Authentication

MakePlans uses HTTP Basic Auth. The client has to enable the API first and you will find the API-Key in the account settings. The API-Key is the username and there is no password. MakePlans uses SSL and all requests over http will be redirected to https.

If your app is installable by end-users you should use oAuth. However we do not yet support oAuth so please contact us.

## Identification

You must include a User-Agent HTTP-header with the name of your application and a link to it or your email address so we can get in touch in case you're doing something wrong (so we may warn you before you're blacklisted) or something awesome (so we may congratulate you). Example: `User-Agent: NoPlans (http://noplans.makeplans.no)`.

## Example request and response

```shell
curl -u APIKEY: \
  -H 'User-Agent: NoPlans (http://noplans.makeplans.no)' \
  https://youraccount.makeplans.no/api/v1/resources
```

To create something, it's the same deal except you also have to include the `Content-Type` HTTP-header and the JSON data:

```shell
curl -u APIKEY: \
  -H 'Content-Type: application/json' \
  -H 'User-Agent: NoPlans (http://noplans.makeplans.no)' \
  -d '{ "resource" : { "title": "My new resource!" } }' \
  -X POST \
  https://youraccount.makeplans.no/api/v1/resources
```

## Syncronisation

Synchronisation with another system can cause issues. First pick either MakePlans or the other system as a master. If the other system is chosen as a master then we recommend the following:

The 'confirmation by administrator' setting for bookings should be enabled on the account. The synchronisation should then retrieve unprocessed bookings and process them (confirm/decline). This will ensure you can handle any changes occured in the other system since the last syncronisation with MakePlans. New unprocessed bookings must be processed often (every 1-5 minutes) to ensure confirmations are sent out quickly to the end-user after requesting a new reservation.

*NEVER* delete any data in MakePlans to make it easier to adapt to the other system. MakePlans is a customer facing booking application. End-users (stored as people) can change and cancel bookings, thus any modifications or destruction of core data should not occur. Bookings are stored with a history (log) and there is a link between a person and bookings. MakePlans make use of this data and all this data must be kept to ensure the booking process in MakePlans works as expected for the end-user.

Expect all booking and person data to be changed at any time.

We recommened storing a timestamp for when the syncronisation was last performed. When the syncronisation is performed again this timestamp can be used to fetch any changes (i.e. the paramater `since` for bookings).

## Client libraries

* CakePHP: https://github.com/espen/CakePHP-MakePlans-Plugin

## Errors

4xx errors means you made a mistake and you need to adjust your request.

### 400 - Bad request

*Not yet implemented*

Please supply identification for your application.

### 401 - Unauthorized

Authentication error. Body will give explanation if there is authorisation issue or if the API is not enabled.

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

Obviously incorrect paths (`/cats`) returns 404. However, even though cool URIs should not change, previously available objects (i.e. `/resources/666`) might have been deleted and thus return a 404 when requested. In most cases deleted resources will be returned and have a booking state or a flag that indicate that the resource is inactive or deleted.

### 429 - Too Many Requests

*Not yet implemented - limits might change*

You can perform up to 50 requests per 10 second period from the same IP address for the same account. If you exceed this limit, you'll get a 429 Too Many Requests response for subsequent requests. Check the `Retry-After` HTTP-header to see how many seconds to wait before retrying the request.

### 5xx - Server error

System errors (aka we screwed up) returns 5xx errors without any detailed information. We log all system errors, but please contact us if you get this response.

## Pagination

Maximum 100 results are returned per page. Specify page with parameter `page`. Pagination is used for: bookings and people. All other objects return all available items.

## Date handling

All dates are specified in the ISO 8601 format. Timezone is included in the output and specified by the account. It is not necessary to specify timezone in the input as the account timezone will be used.

## Custom data

Custom data are stored as key/value. All values are stored as strings. Custom data can be added to booking and person.

## Web hooks

*Not yet implemented*

A webhook is simply a user-defined callback in the form of an HTTP POST which is invoked when something happens.
So whenever a new booking is created in MakePlans we can send a POST request to the URL you specify. The body will include the new booking.

## Available objects

* Slots
* Bookings
* People
* Services
* Resources
* Providers
* Reports
* Users
* Client

# Slots

Slots are not physical objects in MakePlans. It is a virtual representation of available times based on attributes from resources and services. I.e. if a resource is open 8am to 16pm and selected service has interval of 60 minutes, slots will return an array of all time intervals (8am-9am, 9am-10am etc.) and indicate which recoures are available.

Slots are meant for listing available times on the MakePlans booking page. You can however make bookings at any time and with any length - as long as the resource is available off course.

## Attributes

<table>
<tr><th>Name</th><th>Type</th><th>Description</th></tr>
<tr><td>timestamp</td><td>DateTime</td><td>Start</td></tr>
<tr><td>timestamp_end</td><td>DateTime</td><td>End</td></tr>
<tr><td>formatted_timestamp</td><td>DateTime</td><td>Human readable format</td></tr>
<tr><td>formattet_timestamp_end</td><td>DateTime</td><td>Human readable format</td></tr>
<tr><td>free</td><td>Integer</td><td>Free capacity</td></tr>
<tr><td>open_resources</td><td>Array</td><td>Ids of open resources</td></tr>
<tr><td>available_resources</td><td>Array</td><td>Ids of resources with availability</td></tr>
</table>

## Listing

`GET /services/{id}/slots` will return slots for specified service.

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

### Query Parameters

<table>
<tr><th>Name</th><th>Type</th><th>Description</th></tr>
<tr><td>from</td><td>Date</td><td>Default: today 00:00.</td></tr>
<tr><td>to</td><td>Date</td><td>Default: tomorrow 00:00.</td></tr>
<tr><td>selected_resources</td><td>Array</td><td>Default: all active providers.</td></tr>
<tr><td>only_free</td><td>Boolean</td><td>Only return timeslots with availability</td></tr>
</table>

# Bookings

## Attributes

<table>
<tr><th>Name</th><th>Type</th><th>Description</th></tr>
<tr><td>id</td><td>Integer</td><td>Automatically set</td></tr>
<tr><td>created_by</td><td>Integer</td><td>Automatically set</td></tr>
<tr><td>updated_by</td><td>Integer</td><td>Automatically set</td></tr>
<tr><td>service_id</td><td>Integer</td><td>Not required</td></tr>
<tr><td>resource_id</td><td>Integer</td><td>Required</td></tr>
<tr><td>person_id</td><td>Integer</td><td>Not required</td></tr>
<tr><td>booked_from</td><td>Datetime</td><td>Required</td></tr>
<tr><td>booked_to</td><td>Datetime</td><td>Required</td></tr>
<tr><td>created_at</td><td>Datetime</td><td>Automatically set</td></tr>
<tr><td>updated_at</td><td>Datetime</td><td>Automatically set</td></tr>
<tr><td>expires_at</td><td>Datetime</td><td>Not required</td></tr>
<tr><td>notes</td><td>Text</td><td>Not required</td></tr>
<tr><td>reminder_at</td><td>DateTime</td><td>Not required</td></tr>
<tr><td>reminded_at</td><td>DateTime</td><td>Not required</td></tr>
<tr><td>paid_at</td><td>DateTime</td><td>Not required</td></tr>
<tr><td>external_id</td><td>String</td><td>Not required</td></tr>
<tr><td>google_order_number</td><td>String</td><td>Not required</td></tr>
<tr><td>custom_data</td><td>Array</td><td>Not required.</td></tr>
<tr><td>state</td><td>Text</td><td>Automatically set</td></tr>
<tr><td>active</td><td>Boolean</td><td>Automatically set</td></tr>
<tr><td>person_attributes</td><td>Object</td><td>See person</td></tr>
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

### Normal state flow

The normal booking flow when a customer initiates a new booking starts with awaiting_verification. If verification is required MakePlans sends out email or SMS for the customer to verify. When successfully verified the state is updated to confirmed or awaiting_confirmation if reservations requires confirmation by administrator. For confirmation it is the updated to confirmed as normally if confirmed, and to declined if it is not confirmed.

Bookings that have been confirmed and then cancelled, either by customer or administrator, are set to cancelled.

Bookings that are deleted are set to deleted.

## Active bookings

Bookings with states: awaiting_verification, awaiting_confirmation and confirmed, are considered to be active. Bookings with state awaiting_verification will be updated with state verification_expired after the current time passes expires_at. However updating states rely on automatic tasks so you must use the active attribute to check whether a booking is active or not. Only active bookings will be returned unless you specify: a specific booking, a specific state such as bookings that are awaiting confirmation, to return all bookings for a resource or dates, or for a person.

## Listing

`GET /bookings` will return all active bookings. See query parameters for options.

`GET /bookings/recent` will return all active bookings updated today.

`GET /bookings/upcoming` will return all active bookings from and including `{date}`.

`GET /bookings/agenda` will return all active bookings within `{date}`.

`GET /bookings/unconfirmed` will return all unconfirmed bookings.

`GET /bookings/all` will return all bookings of all states (including declined, cancelled, expired and deleted).

Response

```json
[
    {
        "booking": {
            "booked_from": "2012-09-29T07:00:00+02:00",
            "booked_to": "2012-09-29T08:00:00+02:00",
            "created_at": "2012-09-20T15:34:16+02:00",
            "custom_data": {},
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

### Query Parameters

<table>
<tr><th>Name</th><th>Type</th><th>Description</th></tr>
<tr><td>service_id</td><td>Integer</td><td></td></tr>
<tr><td>resource_id</td><td>Integer</td><td></td></tr>
<tr><td>external_id</td><td>String</td><td></td></tr>
<tr><td>start</td><td>DateTime</td><td>booked_from after param</td></tr>
<tr><td>end</td><td>DateTime</td><td>booked_to before param</td></tr>
<tr><td>since</td><td>DateTime</td><td>updated_at after param</td></tr>
</table>

## Add new booking

`POST /bookings` will create a new booking

Example JSON request

Example JSON response

### Add new booking with new person

To add a new person along with a booking you must use populate `person_attributes`. MakePlans will match to an existing person based on email or phone number (in that order).

## Process bookings awaiting confirmation

`PUT /bookings/{booking_id}/confirm` will confirm a booking.

`PUT /bookings/{booking_id}/decline` will decline a booking.

## Cancel a booking

`PUT /bookings/{booking_id}/cancel` will cancel a booking.

## Update booking

`PUT /bookings/{booking_id}` will update existing booking with id `{booking_id}`.

## Delete booking

`DELETE /bookings/{booking_id}` will delete existing booking with id `{booking_id}`.

Deleting a booking will set it to state=deleted and active=false. It will not be visible in listing, only when requesting `GET /bookings/all` or by requesting the booking directly `GET /bookings/{booking_id}`.

Do not use this method if the booking is rescheduled or cancelled.

# People

The primary key for a person is `id`. However the following fields are unique: `email`, `phone number` and `provider`+`uid`. There are no specific requirements for input but a person needs to have either name, email or phone number.

*Not yet implemented*

`National Id No` is currently not unique or used as identificator for a person. This will change.

## Attributes

<table>
<tr><th>Name</th><th>Type</th><th>Description</th></tr>
<tr><td>id</td><td>Integer</td><td>Automatically set</td></tr>
<tr><td>created_at</td><td>Datetime</td><td>Automatically set</td></tr>
<tr><td>updated_at</td><td>Datetime</td><td>Automatically set</td></tr>
<tr><td>name</td><td>String</td><td>Not required*</td></tr>
<tr><td>email</td><td>String</td><td>Not required*</td></tr>
<tr><td>phone_number</td><td>String</td><td>Not required*. Phone number as stored.</td></tr>
<tr><td>phone_number_formatted</td><td>String</td><td>Not required. Phone number with international code and no spaces.</td></tr>
<tr><td>external_id</td><td>String</td><td>Not required</td></tr>
<tr><td>custom_data</td><td>Array</td><td>Not required. Key/value. Stored as strings.</td></tr>
<tr><td>date_of_birth</td><td>Date</td><td>Not required</td></tr>
<tr><td>National_id_no</td><td>String</td><td>Not required.</td></tr>
</table>

## Listing

Searching unique fields (email, phonenumber, external_id, uid+provider) will return only one result if found.

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
            "phonenumber": "",
            "updated_at": "2012-09-20T15:34:16+02:00",
            "phone_number_formatted": null
        }
    }
]
```

### Query Parameters

<table>
<tr><th>Name</th><th>Type</th><th>Description</th></tr>
<tr><td>email</td><td>String</td><td></td></tr>
<tr><td>phonenumber</td><td>String</td><td></td></tr>
<tr><td>external_id</td><td>String</td><td></td></tr>
<tr><td>name</td><td>String</td><td></td></tr>
<tr><td>date_of_birth</td><td>Date</td><td></td></tr>
<tr><td>national_id_no</td><td>String</td><td></td></tr>
</table>

# Services

## Attributes

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
</table>

## Listing

Response

```json
[
    {
        "service": {
            "active": true,
            "booking_capacity": 1,
            "created_at": "2012-09-20T15:34:16+02:00",
            "day_booking_specify_time": false,
            "has_day_booking": false,
            "id": 1,
            "interval": 20,
            "max_slots": null,
            "price": "115.0",
            "same_day": false,
            "title": "Chiropractor",
            "updated_at": "2012-09-20T15:34:16+02:00"
        }
    }
]
```

# Resources

## Attributes

<table>
<tr><th>Name</th><th>Type</th><th>Description</th></tr>
<tr><td>id</td><td>Integer</td><td>Automatically set</td></tr>
<tr><td>created_at</td><td>Datetime</td><td>Automatically set</td></tr>
<tr><td>updated_at</td><td>Datetime</td><td>Automatically set</td></tr>
<tr><td>title</td><td>String</td><td>Required</td></tr>
<tr><td>active</td><td>Boolean</td><td>Automatically set</td></tr>
<tr><td>open_0</td><td>Time</td><td></td></tr>
<tr><td>open_1</td><td>Time</td><td></td></tr>
<tr><td>open_2</td><td>Time</td><td></td></tr>
<tr><td>open_3</td><td>Time</td><td></td></tr>
<tr><td>open_4</td><td>Time</td><td></td></tr>
<tr><td>open_5</td><td>Time</td><td></td></tr>
<tr><td>open_6</td><td>Time</td><td></td></tr>
<tr><td>close_0</td><td>Time</td><td></td></tr>
<tr><td>close_1</td><td>Time</td><td></td></tr>
<tr><td>close_2</td><td>Time</td><td></td></tr>
<tr><td>close_3</td><td>Time</td><td></td></tr>
<tr><td>close_4</td><td>Time</td><td></td></tr>
<tr><td>close_5</td><td>Time</td><td></td></tr>
<tr><td>close_6</td><td>Time</td><td></td></tr>
</table>

## Listing

Response

```json
[
    {
        "resource": {
            "capacity": 1,
            "close_0": "20:00",
            "close_1": "20:00",
            "close_2": "20:00",
            "close_3": "20:00",
            "close_4": "20:00",
            "close_5": null,
            "close_6": null,
            "created_at": "2012-09-20T15:34:16+02:00",
            "id": 1,
            "open_0": "10:00",
            "open_1": "10:00",
            "open_2": "10:00",
            "open_3": "10:00",
            "open_4": "10:00",
            "open_5": null,
            "open_6": null,
            "title": "Mr. Spine Twister",
            "updated_at": "2012-09-20T15:34:16+02:00"
        }
    }
]
```

# Providers

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
</table>

## Listing

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

# Client

Information and settings for your account.

## Attributes

<table>
<tr><th>Name</th><th>Type</th><th>Description</th></tr>
<tr><td>id</td><td>Integer</td><td>Automatically set</td></tr>
<tr><td>created_at</td><td>Datetime</td><td>Automatically set</td></tr>
<tr><td>updated_at</td><td>Datetime</td><td>Automatically set</td></tr>
<tr><td>name</td><td>String</td><td>Required</td></tr>
</table>