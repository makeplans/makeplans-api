# Private API Examples.

Please familiarise yourself with the key concept of the MakePlans API first.

## List available services

First we want to show a list of available services on the booking site.

<details>
<summary>Request</summary>

```curlrc
curl "https://youraccount.test.makeplans.net/services" \
     -H 'User-Agent: YourAppName (http://example.org)' \
     -H 'Accept: application/json'
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
      "booking_type_id": 1,
      "description": null,
      "custom_data": null
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
      "booking_type_id": 1,
      "description": null,
      "custom_data": null
    }
  }
]
```
</details>

## Find available timeslots for a service

The user select 'Massage' so lets get all available timeslots for 'Massage' (service_id 15).

By default this will return timeslots for today, but you can specify the timeframe using from/to parameters.

<details>
<summary>Request</summary>

```curlrc
curl "https://youraccount.test.makeplans.net/services/15/slots" \
     -H 'User-Agent: YourAppName (http://example.org)' \
     -H 'Accept: application/json'
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