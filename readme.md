# MakePlans API - Version 1.0

MakePlans provides a fairly standard REST API. The base URL is `https://youraccount.makeplans.no/api/`. All requests are done over HTTPS.

All data is UTF-8.

## Versions

The current version of the API is version 1. The versioning scheme is as follows: `/api/v1/` The most recent version of the API is always at `/api/`. So all calls to `/api/resources` are redirected to the current version, at the time being: `/api/v1/resources`.

Please keep up to date with the API as older versions may be deprecated.

All paths in the rest of this document uses `/api/v1/` as base path.

## Formats

The API supports JSON and XML for input and output.

### Output

To specify JSON as output use HTTP-header `Accept: application/json`. Output can also be defined by using extension in the path: `/resources.json`. Multiple items is returned as an array.

### Input

To specify JSON as input use HTTP-header `Content-Type: application/json`. The body must be JSON-formatted and include the object with required attributes. Alternatively you can use normal form data as input, in that case do not specify the Content-type HTTP-header. Form values are specified like this: `resource[title]=Unicorn`. Normal form input is preferred over JSON/XML.

All parameters that are not input for creating or updating objects should be sent as normal URL parameters. Example: `/bookings?page=2&resources_id=1`.

## Authentication

MakePlans uses HTTP Basic Auth. The client has to enable the API first and you will find the API-Key in the account settings. The API-Key is the username and there is no password. MakePlans uses SSL and all requests over http will be redirected to https.

If your app is installable by end-users you should use oAuth. However we do not yet support oAuth so please contact us.

## Identification

*Not yet implemented*

You must include a User-Agent header with the name of your application and a link to it or your email address so we can get in touch in case you're doing something wrong (so we may warn you before you're blacklisted) or something awesome (so we may congratulate you). Example: `User-Agent: NoPlans (http://noplans.makeplans.no)`. If you don't supply this HTTP-header, you will get a 400 Bad Request.

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

## Client libraries

* CakePHP: https://github.com/espen/CakePHP-MakePlans-Plugin


## Errors

### 400 - Bad request

Please supply identification for your application.

### 401 - Unauthorized

Authentication error. Body will give explanation if there is authorisation issue or if the API is not enabled.

### 402 - Payment Required

Trial period expired. Please pay your bill.

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

A webhook is simply a user-defined callback in the form of an HTTP POST, which is invoked when something happens.
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

Slots are not a physical object in MakePlans. It is a virtual representation of available times based on attributes from resources and services. I.e. if a resource is open 8am to 16pm and selected service has interval of 60 minutes, slots will return an array of all available times. If there is a booking on the specified date that time will not be returned. So if there only is a booking between 1pm to 2pm these slots will be returned: 8am, 9am, 10am, 11am, 12pm, 2pm, 3pm.

Slots are meant for listing available times on the MakePlans booking-page. You can however make bookings at any time and with any length - as long as the resource is available off course.

# Bookings

## Attributes

<table>
<tr><td>Id</td><td>Integer</td><td>Automatically set</td></tr>
<tr><td>Service_id</td><td>Integer</td><td>Not required</td></tr>
<tr><td>Resource_id</td><td>Integer</td><td>Required</td></tr>
<tr><td>Person_id</td><td>Integer</td><td>Not required</td></tr>
<tr><td>Booked_from</td><td>Datetime</td><td>Required</td></tr>
<tr><td>Booked_to</td><td>Datetime</td><td>Required</td></tr>
<tr><td>Created_at</td><td>Datetime</td><td>Automatically set</td></tr>
<tr><td>Updated_at</td><td>Datetime</td><td>Automatically set</td></tr>
<tr><td>Expires_at</td><td>Datetime</td><td>Not required</td></tr>
<tr><td>Notes</td><td>Text</td><td>Not required</td></tr>
<tr><td>Reminder_at</td><td>DateTime</td><td>Not required</td></tr>
<tr><td>Reminded_at</td><td>DateTime</td><td>Not required</td></tr>
<tr><td>Paid_at</td><td>DateTime</td><td>Not required</td></tr>
<tr><td>External_id</td><td>String</td><td>Not required</td></tr>
<tr><td>Google_order_number</td><td>String</td><td>Not required</td></tr>
<tr><td>Custom_data</td><td>Array</td><td>Not required.</td></tr>
<tr><td>Created_by</td><td>Integer</td><td>Automatically set</td></tr>
<tr><td>Updated_by</td><td>Integer</td><td>Automatically set</td></tr>
<tr><td>State</td><td>Text</td><td>Automatically set</td></tr>
<tr><td>Active</td><td>Boolean</td><td>Automatically set</td></tr>
<tr><td>Person attributes</td><td>Object</td><td>See person</td></tr>
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

Bookings with states: awaiting_verification, awaiting_confirmation and confirmed, are considered to be active. Bookings with state awaiting_verification will be updated wto state verification_expired after the current time passes expires_at. However updating states rely on automatic tasks so you must use the active attribute to check whether a booking is active or not. Only active bookings will be returned unless you specify: a specific booking, a specific state such as bookings that are awaiting confirmation, to return all bookings for a resource or dates, or for a person.

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
            "id": 23,
            "notes": "NYYYYY",
            "person_id": 18,
            "resource_id": 1,
            "service_id": 1,
            "updated_at": "2012-09-20T15:34:16+02:00"
        }
    },
    {
        "booking": {
            "booked_from": "2012-09-28T07:00:00+02:00",
            "booked_to": "2012-09-28T08:00:00+02:00",
            "created_at": "2012-09-20T15:26:03+02:00",
            "custom_data": {},
            "expires_at": null,
            "external_id": null,
            "id": 20,
            "notes": "feafa",
            "person_id": 15,
            "resource_id": 1,
            "service_id": 1,
            "updated_at": "2012-09-20T15:26:03+02:00"
        }
    }
]
```

### Query Parameters

<table>
<tr><td>service_id</td><td>Integer</td><td>service_id</td><td></td></tr>
<tr><td>resource_id</td><td>Integer</td><td>resource_id</td><td></td></tr>
<tr><td>external_id</td><td>Integer</td><td>external_id</td><td></td></tr>
<tr><td>start</td><td>DateTime</td><td>booked_from</td><td>Query bookings within datetime range.</td></tr>
<tr><td>end</td><td>DateTime</td><td>booked_to</td><td>Query bookings within datetime range.</td></tr>
<tr><td>since</td><td>DateTime</td><td>updated_at</td><td>Query bookings after timestamp.</td></tr>
</table>

## Add new booking

`POST /bookings` will create a new booking

Example JSON request

Example JSON response

### Add new booking with new person

To add a new person along with a booking you must use populate `person_attributes`. MakePlans will match to an existing person based on email or phone number (in that order).

## Confirmation

`PUT /bookings/{booking_id}/confirm` will confirm a booking.

`PUT /bookings/{booking_id}/decline` will decline a booking.

## Update booking

`PUT /bookings/{booking_id}` will update existing booking with id `{booking_id}`.

## Delete booking

`DELETE /bookings/{booking_id}` will delete existing booking with id `{booking_id}`.

Deleting a booking will set it to state=deleted and active=false. It will not be visible in listing, only when requesting `GET /bookings/all` or by requesting the booking directly `GET /bookings/{booking_id}`.

# People

The primary key for a person is the ID. However the following fields are unique: email, phone number and provider+uid. There are no specific requirements for input but a person needs to have either name, email or phone number.

*Not yet implemented*

External_id currently not unique in our database. Due to external circrumstances it will remain so until those issues are fixed. We plan to constrain external_id to be unique and you should treat it as so.

## Attributes

<table>
<tr><td>Id</td><td>Integer</td><td>Automatically set</td></tr>
<tr><td>Created_at</td><td>Datetime</td><td>Automatically set</td></tr>
<tr><td>Updated_at</td><td>Datetime</td><td>Automatically set</td></tr>
<tr><td>Name</td><td>String</td><td>Not required*</td></tr>
<tr><td>Email</td><td>String</td><td>Not required*</td></tr>
<tr><td>Phone number</td><td>String</td><td>Not required*. Phone number as stored.</td></tr>
<tr><td>Phone number formatted</td><td>String</td><td>Not required. Phone number with international code and no spaces.</td></tr>
<tr><td>External id</td><td>String</td><td>Not required</td></tr>
<tr><td>Custom Data</td><td>Array</td><td>Not required. Key/value. Stored as strings.</td></tr>
<tr><td>Date Of Birth</td><td>Datetime</td><td>Not required</td></tr>
</table>

## Listing

Searching unique fields (email, phonenumber, external_id, uid+provider) will return only one result if found.

### Query Parameters

<table>
<tr><td>email</td><td>String</td><td>email</td><td>Only exact match</td></tr>
<tr><td>phonenumber</td><td>String</td><td>phonenumber</td><td>Only exact match</td></tr>
<tr><td>external_id</td><td>String</td><td>external_id</td><td>Only exact match</td></tr>
<tr><td>name</td><td>String</td><td>name</td><td></td></tr>
<tr><td>date_of_birth</td><td>Date</td><td>date_of_birth</td><td></td></tr>
</table>

# Services

## Attributes

<table>
<tr><td>Id</td><td>Integer</td><td>Automatically set</td></tr>
<tr><td>Created_at</td><td>Datetime</td><td>Automatically set</td></tr>
<tr><td>Updated_at</td><td>Datetime</td><td>Automatically set</td></tr>
<tr><td>Title</td><td>String</td><td>Required</td></tr>
<tr><td>Active</td><td>Boolean</td><td>Automatically set</td></tr>
<tr><td>Booking capacity</td><td>Integer</td><td>Not required</td></tr>
<tr><td>Day booking specify time</td><td>Boolean</td><td>Not Required (default false)</td></tr>
<tr><td>Has day booking</td><td>Boolean</td><td>Not required (default false)</td></tr>
<tr><td>Interval</td><td>Integer</td><td>Not required (default 60)</td></tr>
<tr><td>Max slots</td><td>Integer</td><td>Not required (default 1)</td></tr>
<tr><td>Price</td><td>Decimal</td><td>Not required. Uses currency on client.</td></tr>
<tr><td>Same day</td><td>Boolean</td><td>Not required (default false)</td></tr>
</table>

# Resources

## Attributes

<table>
<tr><td>Id</td><td>Integer</td><td>Automatically set</td></tr>
<tr><td>Created_at</td><td>Datetime</td><td>Automatically set</td></tr>
<tr><td>Updated_at</td><td>Datetime</td><td>Automatically set</td></tr>
<tr><td>Title</td><td>String</td><td>Required</td></tr>
<tr><td>Active</td><td>Boolean</td><td>Automatically set</td></tr>
<tr><td>Open_0</td><td>Time</td><td></td></tr>
<tr><td>Open_1</td><td>Time</td><td></td></tr>
<tr><td>Open_2</td><td>Time</td><td></td></tr>
<tr><td>Open_3</td><td>Time</td><td></td></tr>
<tr><td>Open_4</td><td>Time</td><td></td></tr>
<tr><td>Open_5</td><td>Time</td><td></td></tr>
<tr><td>Open_6</td><td>Time</td><td></td></tr>
<tr><td>Close_0</td><td>Time</td><td></td></tr>
<tr><td>Close_1</td><td>Time</td><td></td></tr>
<tr><td>Close_2</td><td>Time</td><td></td></tr>
<tr><td>Close_3</td><td>Time</td><td></td></tr>
<tr><td>Close_4</td><td>Time</td><td></td></tr>
<tr><td>Close_5</td><td>Time</td><td></td></tr>
<tr><td>Close_6</td><td>Time</td><td></td></tr>
</table>

# Providers

Resources provides services. This link is called a provider.

## Attributes

<table>
<tr><td>Id</td><td>Integer</td><td>Automatically set</td></tr>
<tr><td>Created_at</td><td>Datetime</td><td>Automatically set</td></tr>
<tr><td>Updated_at</td><td>Datetime</td><td>Automatically set</td></tr>
<tr><td>Active</td><td>Boolean</td><td>Automatically set</td></tr>
<tr><td>Resource Id</td><td>Integer</td><td>Required</td></tr>
<tr><td>Service Id</td><td>Integer</td><td>Required</td></tr>
</table>

# Client

Information and settings for your account.

## Attributes

<table>
<tr><td>Id</td><td>Integer</td><td>Automatically set</td></tr>
<tr><td>Created_at</td><td>Datetime</td><td>Automatically set</td></tr>
<tr><td>Updated_at</td><td>Datetime</td><td>Automatically set</td></tr>
</tabøe>