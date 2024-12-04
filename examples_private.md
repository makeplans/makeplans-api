# Private API Examples

Please familiarise yourself with the key concepts of the MakePlans API first.

## Public booking site examples

### List available services

First we want to show a list of available services on the booking site.

<details>
<summary>Request</summary>

```curlrc
curl "https://youraccount.test.makeplans.net/api/v1/services" \
     -H 'User-Agent: YourAppName (http://example.org)' \
     -H 'Accept: application/json' \
     -u 'YOURAPIKEY:'
```
</details>

<details>
<summary>Response</summary>

```http
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

```json
[
  {
    "service": {
      "id": 16,
      "title": "Body scrub",
      "created_at": "2020-01-24T06:24:16+01:00",
      "updated_at": "2020-01-24T06:25:15+01:00",
      "interval": 45,
      "has_day_booking": false,
      "booking_capacity": 1,
      "day_booking_specify_time": null,
      "max_slots": 1,
      "same_day": false,
      "price": null,
      "active": true,
      "interval_rounding": null,
      "template": null,
      "booking_type_id": 1,
      "description": null,
      "custom_data": null,
      "mail_confirmation": null,
      "mail_verification": null,
      "sms_confirmation": null,
      "sms_verification": null,
      "sms_reminder": null,
      "booking_form": null
    }
  },
  {
    "service": {
      "id": 15,
      "title": "Massage",
      "created_at": "2020-01-24T06:24:16+01:00",
      "updated_at": "2020-01-24T06:25:15+01:00",
      "interval": 60,
      "has_day_booking": false,
      "booking_capacity": 1,
      "day_booking_specify_time": null,
      "max_slots": 1,
      "same_day": false,
      "price": null,
      "active": true,
      "interval_rounding": null,
      "template": null,
      "booking_type_id": 1,
      "description": null,
      "custom_data": null,
      "mail_confirmation": null,
      "mail_verification": null,
      "sms_confirmation": null,
      "sms_verification": null,
      "sms_reminder": null,
      "booking_form": null
    }
  }
]
```
</details>

### Find available timeslots for a service

The user select 'Massage' so lets get all available timeslots for 'Massage' (service_id 15).

By default this will return timeslots for today, but you can specify the timeframe using from/to parameters.

<details>
<summary>Request</summary>

```curlrc
curl "https://youraccount.test.makeplans.net/api/v1/services/15/slots" \
     -H 'User-Agent: YourAppName (http://example.org)' \
     -H 'Accept: application/json' \
     -u 'YOURAPIKEY:'
```
</details>

<details>
<summary>Response</summary>

```http
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

```json
[
  {
    "slot": {
      "timestamp": "2020-01-24T09:00:00+01:00",
      "timestamp_end": "2020-01-24T10:00:00+01:00",
      "formatted_timestamp": "Friday, 24 January 2020, 09:00",
      "formatted_timestamp_end": "Friday, 24 January 2020, 10:00",
      "free": 3,
      "available_resources": [
        10,
        11,
        12
      ],
      "maximum_capacity": 3
    }
  },
  {
    "slot": {
      "timestamp": "2020-01-24T10:00:00+01:00",
      "timestamp_end": "2020-01-24T11:00:00+01:00",
      "formatted_timestamp": "Friday, 24 January 2020, 10:00",
      "formatted_timestamp_end": "Friday, 24 January 2020, 11:00",
      "free": 3,
      "available_resources": [
        10,
        11,
        12
      ],
      "maximum_capacity": 3
    }
  },
  # More items not shown
]
```
</details>

### Initiate new booking for new customer

Now the customer is ready to book. So existing!

This is the first example where you will differentiate the request since the goal is to implement a custom booking site. The MakePlans booking site operates based on many settings and rules that define availability. Naturally existing bookings is taken into account so you do not get double bookings (it is possible to add double bookings, but not from the public booking site). And then there is the availability set for the resources; standard opening hours and opening hours set on specific dates. In addition you can set certain rules for when booking is allowed, for example only 2 months ahead or not the same day. You can also set the booking site to be closed during national holidays.
But back to the API. When you make a request to create a new booking in the private API we will ignore most of these rules. These rules are only applicable on the public booking site. So it is very important that you add `public_booking=true` to the request to ensure booking are only allowed during the rules and availability you set on your account and resources. Unless you are authenticated your customers in your app it is also recommended that you let MakePlans handle verification. We will send an SMS that the customer needs to verify to confirm the booking.

Please note: MakePlans will automatically match email and phone number to existing customer profile.

<details>
<summary>Request</summary>

```curlrc
curl -X "POST" "https://youraccount.test.makeplans.net/api/v1/bookings" \
     -H 'User-Agent: YourAppName (http://example.org)' \
     -H 'Accept: application/json' \
     -H 'Content-Type: application/x-www-form-urlencoded; charset=utf-8' \
     -u 'YOURAPIKEY:' \
     --data-urlencode "booking[service_id]=15" \
     --data-urlencode "booking[booked_from]=2020-01-27 14:00" \
     --data-urlencode "booking[booked_to]=2020-01-27 15:00" \
     --data-urlencode "booking[person_attributes][name]=Harald" \
     --data-urlencode "booking[person_attributes][phone_number]=+18338367888" \
     --data-urlencode "booking[public_booking]=true" \
     --data-urlencode "confirm=false"
```
</details>

<details>
<summary>Response</summary>

```http
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

```json
{
  "booking": {
    "id": 208,
    "service_id": 15,
    "person_id": 95,
    "booked_from": "2020-01-27T14:00:00+01:00",
    "booked_to": "2020-01-27T15:00:00+01:00",
    "created_at": "2020-01-24T06:48:42+01:00",
    "updated_at": "2020-01-24T06:48:42+01:00",
    "verification_code": "75455",
    "expires_at": "2020-01-24T07:08:42+01:00",
    "count": 1,
    "notes": null,
    "resource_id": 11,
    "reminder_at": "2020-01-26T12:00:00+01:00",
    "reminded_at": null,
    "paid_at": null,
    "external_id": null,
    "state": "awaiting_verification",
    "verification_method": "sms",
    "custom_data": null,
    "revision_count": 0,
    "paid_amount": null,
    "event_id": null,
    "collection_id": null,
    "invoiced_at": null,
    "active": true,
    "person": {
      "id": 95,
      "email": null,
      "phonenumber": "+18338367888",
      "created_at": "2020-01-24T06:48:42+01:00",
      "updated_at": "2020-01-24T06:48:42+01:00",
      "name": "Harald",
      "custom_data": null,
      "date_of_birth": null,
      "external_id": null,
      "national_id_no": null,
      "street": null,
      "city": null,
      "postal_code": null,
      "state": null,
      "country_code": null,
      "notes": null
    },
    "resource": {
      "id": 11,
      "title": "Thor"
    },
    "service": {
      "id": 15,
      "title": "Massage"
    }
  }
}
```
</details>

So we have a tentative booking! As you can see the booking state is set to `awaiting_verification`. This means we have to verify the booking. Make sure you save the `id` of the booking for the next step.

### Verify booking

By now the customer should have received the SMS verification code.

As you can see from the response in the previous request the verification code in this booking is 75455.

<details>
<summary>Request</summary>

```curlrc
curl -X "PUT" "https://youraccount.test.makeplans.net/api/v1/bookings/208/verify_code" \
     -H 'User-Agent: YourAppName (http://example.org)' \
     -H 'Accept: application/json' \
     -H 'Content-Type: application/x-www-form-urlencoded; charset=utf-8' \
     -u 'YOURAPIKEY:' \
     --data-urlencode "verification_code=75455"
```
</details>

<details>
<summary>Response</summary>

```http
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

```json
{
  "booking": {
    "id": 208,
    "verification_code": "75455",
    "state": "confirmed",
    "event_id": null,
    "service_id": 15,
    "expires_at": null,
    "resource_id": 11,
    "person_id": 95,
    "booked_from": "2020-01-27T14:00:00+01:00",
    "booked_to": "2020-01-27T15:00:00+01:00",
    "reminder_at": "2020-01-26T12:00:00+01:00",
    "count": 1,
    "reminded_at": null,
    "created_at": "2020-01-24T06:48:42+01:00",
    "updated_at": "2020-01-24T06:56:09+01:00",
    "notes": null,
    "paid_at": null,
    "external_id": null,
    "verification_method": "sms",
    "custom_data": null,
    "revision_count": 1,
    "paid_amount": null,
    "collection_id": null,
    "invoiced_at": null,
    "active": true,
    "person": {
      "id": 95,
      "email": null,
      "phonenumber": "+18338367888",
      "created_at": "2020-01-24T06:48:42+01:00",
      "updated_at": "2020-01-24T06:48:42+01:00",
      "name": "Harald",
      "custom_data": null,
      "date_of_birth": null,
      "external_id": null,
      "national_id_no": null,
      "street": null,
      "city": null,
      "postal_code": null,
      "state": null,
      "country_code": null,
      "notes": null
    },
    "resource": {
      "id": 11,
      "title": "Thor"
    },
    "service": {
      "id": 15,
      "title": "Massage"
    }
  }
}
```
</details>

The booking is now confirmed. MakePlans will now send an SMS confirmation to the customer (based on account settings, you can also set this per request).

If the customers enters an incorrect code we will return 403 status:

<details>
<summary>Response</summary>

```http
HTTP/1.1 403 Forbidden
Content-Type: application/json; charset=utf-8
```

```json
{
  "verification_code": [
    "is invalid"
  ]
}
```
</details>

### Create new booking with custom data for new customer

<details>
<summary>Request</summary>

```http
curl -X "POST" "https://youraccount.test.makeplans.net/api/v1/bookings" \
     -H 'User-Agent: YourAppName (http://example.org)' \
     -H 'Accept: application/json' \
     -H 'Content-Type: application/x-www-form-urlencoded; charset=utf-8' \
     -u 'YOURAPIKEY:' \
     --data-urlencode "booking[service_id]=15" \
     --data-urlencode "booking[booked_from]=2020-01-27 14:00" \
     --data-urlencode "booking[booked_to]=2020-01-27 15:00" \
     --data-urlencode "booking[person_attributes][name]=Harald" \
     --data-urlencode "booking[person_attributes][phone_number]=+18338367888" \
     --data-urlencode "booking[public_booking]=true" \
     --data-urlencode "confirm=false" \
     --data-urlencode "booking[custom_data][problems]=Right leg" \
     --data-urlencode "booking[person_attributes][custom_data][member_id]=83632"
```
</details>

<details>
<summary>Response</summary>

```http
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

```json
{
  "booking": {
    "id": 209,
    "service_id": 15,
    "person_id": 95,
    "booked_from": "2020-01-27T14:00:00+01:00",
    "booked_to": "2020-01-27T15:00:00+01:00",
    "created_at": "2020-01-24T07:01:50+01:00",
    "updated_at": "2020-01-24T07:01:50+01:00",
    "verification_code": "82691",
    "expires_at": "2020-01-24T07:21:50+01:00",
    "count": 1,
    "notes": null,
    "resource_id": 12,
    "reminder_at": "2020-01-26T12:00:00+01:00",
    "reminded_at": null,
    "paid_at": null,
    "external_id": null,
    "state": "awaiting_verification",
    "verification_method": "sms",
    "custom_data": {
      "problems": "Right leg"
    },
    "revision_count": 0,
    "paid_amount": null,
    "event_id": null,
    "collection_id": null,
    "invoiced_at": null,
    "active": true,
    "person": {
      "id": 95,
      "email": null,
      "phonenumber": "+18338367888",
      "created_at": "2020-01-24T06:48:42+01:00",
      "updated_at": "2020-01-24T06:48:42+01:00",
      "name": "Harald",
      "custom_data": {
        "member_id": "83632"
      },
      "custom_data": null,
      "date_of_birth": null,
      "external_id": null,
      "national_id_no": null,
      "street": null,
      "city": null,
      "postal_code": null,
      "state": null,
      "country_code": null,
      "notes": null
    },
    "resource": {
      "id": 12,
      "title": "Astrid"
    },
    "service": {
      "id": 15,
      "title": "Massage"
    }
  }
}
```
</details>

### Create a recurring booking

<details>
<summary>Request</summary>

```http
curl -X "POST" "https://youraccount.test.makeplans.net/api/v1/bookings/recurring" \
     -H 'User-Agent: YourAppName (http://example.org)' \
     -H 'Accept: application/json' \
     -H 'Content-Type: application/x-www-form-urlencoded; charset=utf-8' \
     -u 'YOURAPIKEY:' \
     --data-urlencode "booking[service_id]=15" \
     --data-urlencode "booking[booked_from]=2020-01-27 14:00" \
     --data-urlencode "booking[booked_to]=2020-01-27 15:00" \
     --data-urlencode "booking[person_attributes][name]=Harald" \
     --data-urlencode "booking[person_attributes][phone_number]=+18338367888" \
     --data-urlencode "booking[public_booking]=true" \
     --data-urlencode "confirm=false" \
     --data-urlencode "confirmation_send_email=true" \
     --data-urlencode "recurring[rrule]=FREQ=DAILY;UNTIL=20241008T000000Z"
```
</details>

<details>
<summary>Response</summary>

```http
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

```json
{
  "collection_id": "1337"
}
```
</details>

### Create new booking with a self pre-authenticated customer

### Authenticate customer

## Full integration examples (Setup and syncronization)

### Create new service

### Create new booking outside normal opening hours

### Delete booking

