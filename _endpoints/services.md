---
title: Services
nav_order: 4
description: API reference for the Makeplans services endpoint — service types and their attributes.
---

There are seven types of services:

* Appointment
* Attendance
* Product
* Gift card
* Addon
* Subscription
* Pass

Appointments can be booked within fixed opening hours as specified on the Resource and with exceptions specified in ResourceExceptionDate.

Attendance at an event is also a booking but the individual booking datetime or resource cannot be modified. Attendance is linked to an event which occurs at a specific time. One service can be linked to one or multiple events.

## Attributes

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>id</td><td>Integer</td><td>Automatically set</td></tr>
  <tr><td>created_at</td><td>Datetime</td><td>Automatically set</td></tr>
  <tr><td>updated_at</td><td>Datetime</td><td>Automatically set</td></tr>
  <tr><td>title</td><td>String</td><td>Required</td></tr>
  <tr><td>description</td><td>Text</td><td></td></tr>
  <tr><td>active</td><td>Boolean</td><td>Automatically set</td></tr>
  <tr><td>booking_capacity</td><td>Integer</td><td>Not required</td></tr>
  <tr><td>day_booking_specify_time</td><td>Boolean</td><td>Not required</td></tr>
  <tr><td>has_day_booking</td><td>Boolean</td><td>Not required (default false)</td></tr>
  <tr><td>interval</td><td>Integer</td><td>Not required (default 60)</td></tr>
  <tr><td>max_slots</td><td>Integer</td><td>Not required (default 1)</td></tr>
  <tr><td>price</td><td>Decimal</td><td>Not required.</td></tr>
  <tr><td>price_rules</td><td>Array</td><td>Prices at given weekdays and times. See <a href="#price-rules">price rules</a>. Only for output.</td></tr>
  <tr><td>price_rules_attributes</td><td>Array</td><td>See <a href="#price-rules">price rules</a>. Only for input.</td></tr>
  <tr><td>same_day</td><td>Boolean</td><td>Not required (default false)</td></tr>
  <tr><td>template</td><td>String</td><td>Component template (calendar view)</td></tr>
  <tr><td>interval_rounding</td><td>Integer</td><td>Overrides account default (see info on account object)</td></tr>
  <tr><td>booking_type</td><td>String</td><td>Required. Values: appointment (default), attendance, product, gift_card, addon, subscription, pass</td></tr>
  <tr><td>custom_data</td><td>Object</td><td>Not required. Key/value. Stored as strings.</td></tr>
  <tr><td>booking_form</td><td>Liquid-Text</td><td>Custom booking form</td></tr>
  <tr><td>mail_verification</td><td>Liquid-Text</td><td></td></tr>
  <tr><td>mail_confirmation</td><td>Liquid-Text</td><td></td></tr>
  <tr><td>sms_verification</td><td>Liquid-Text</td><td></td></tr>
  <tr><td>sms_confirmation</td><td>Liquid-Text</td><td></td></tr>
  <tr><td>sms_reminder</td><td>Liquid-Text</td><td></td></tr>
  <tr><td>mail_modification</td><td>Liquid-Text</td><td></td></tr>
  <tr><td>mail_awaiting_confirmation</td><td>Liquid-Text</td><td></td></tr>
  <tr><td>mail_cancellation</td><td>Liquid-Text</td><td></td></tr>
  <tr><td>mail_declined</td><td>Liquid-Text</td><td></td></tr>
  <tr><td>sms_modification</td><td>Liquid-Text</td><td></td></tr>
  <tr><td>sms_awaiting_confirmation</td><td>Liquid-Text</td><td></td></tr>
  <tr><td>sms_cancellation</td><td>Liquid-Text</td><td></td></tr>
  <tr><td>sms_declined</td><td>Liquid-Text</td><td></td></tr>
  <tr><td>mail_request_payment</td><td>Liquid-Text</td><td></td></tr>
  <tr><td>sms_request_payment</td><td>Liquid-Text</td><td></td></tr>
  <tr><td>next_interval</td><td>Integer</td><td>Not required</td></tr>
  <tr><td>payment_required</td><td>Boolean</td><td>Not required</td></tr>
  <tr><td>allow_invoice</td><td>Boolean</td><td>Not required</td></tr>
  <tr><td>booking_minimum</td><td>Integer</td><td>Not required</td></tr>
  <tr><td>terms</td><td>Text</td><td>Not required</td></tr>
  <tr><td>priority_strategy</td><td>String</td><td>Not required. Strategy for resource priority assignment.</td></tr>
  <tr><td>availability_type</td><td>String</td><td>Not required. Values: resource (default), provider. With resource the opening hours on the resource are used. With provider the opening hours on the [provider](/endpoints/providers/) are used.</td></tr>
  <tr><td>priority_value</td><td>Integer</td><td>Not required. Priority value for ordering.</td></tr>
  <tr><td>quota</td><td>Integer</td><td>Number of uses. Required for subscription and pass unless `unlimited` is true. Not applicable for other types.</td></tr>
  <tr><td>unlimited</td><td>Boolean</td><td>Unlimited number of uses. Only applicable for subscription and pass. Default: false.</td></tr>
  <tr><td>billing_period</td><td>Integer</td><td>Required for subscription. Not applicable for other types.</td></tr>
  <tr><td>renewal_price</td><td>Decimal</td><td>Only applicable for subscription.</td></tr>
  <tr><td>validity_days</td><td>Integer</td><td>Number of days the pass is valid. Required for unlimited passes. Only applicable for pass.</td></tr>
  <tr><td>workload_points</td><td>Integer</td><td>Not required. Points used per booking towards the daily maximum on the resource.</td></tr>
  <tr><td>linked_service_ids</td><td>Array</td><td>Ids of linked services.</td></tr>
</table>

### Deprecated attributes

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>booking_type_id</td><td>Integer</td><td>1: appointment. 2: attendance. 3: gift_card. 4: product. 5: addon. 6: subscription. 7: pass.</td></tr>
</table>

## Price rules

A service can have a different price at given weekdays and times, for example peak and off-peak. Each price rule is one weekly time block with a fixed price.

- Each slot of a booking gets the price of the rule that covers the start time of the slot.
- Outside all rules the standard `price` of the service applies. `price` on the service does not change.
- Times are in the time zone of the account.
- `ends_at` is exclusive, so a rule from `08:00` to `12:00` and a rule from `12:00` to `16:00` can adjoin.

`price_rules` is included in the service object of the private API when you list, get, add or update a service. A service with no rules returns an empty array. It is not included in the public API, and not in a service that is nested in another object (for example `service` in a booking).

### Price rule attributes

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>id</td><td>Integer</td><td>Automatically set</td></tr>
  <tr><td>created_at</td><td>Datetime</td><td>Automatically set</td></tr>
  <tr><td>updated_at</td><td>Datetime</td><td>Automatically set</td></tr>
  <tr><td>weekdays</td><td>Array</td><td>Required. Integers from 1 (Monday) to 7 (Sunday), as in ISO 8601.</td></tr>
  <tr><td>starts_at</td><td>String</td><td>Required. Time of day, <code>HH:MM</code>.</td></tr>
  <tr><td>ends_at</td><td>String</td><td>Required. Time of day, <code>HH:MM</code>, exclusive. Must be after <code>starts_at</code>. <code>24:00</code> is permitted.</td></tr>
  <tr><td>price</td><td>Decimal</td><td>Required. Price for one slot. Must be more than 0.</td></tr>
</table>

Response

```json
{
  "service": {
    "id": 5,
    "price": "200.0",
    "price_rules": [
      {
        "id": 12,
        "created_at": "2026-11-20T10:00:00+01:00",
        "updated_at": "2026-11-20T10:00:00+01:00",
        "weekdays": [1, 2],
        "starts_at": "17:00",
        "ends_at": "24:00",
        "price": "300.0"
      }
    ]
  }
}
```

### Add, change and remove price rules

Send `price_rules_attributes` in the `service` object when you [add](#add-new-service) or [update](#update-service) a service.

- An entry with no `id` adds a rule.
- An entry with the `id` of an existing rule changes that rule.
- An entry with `id` and `_destroy` set to true removes that rule.
- Rules that are not in the request do not change.

```json
{
  "service": {
    "price_rules_attributes": [
      {
        "weekdays": [1, 2, 3, 4, 5],
        "starts_at": "17:00",
        "ends_at": "24:00",
        "price": 300
      },
      {
        "id": 12,
        "price": 350
      },
      {
        "id": 13,
        "_destroy": true
      }
    ]
  }
}
```

### Validation

A request that breaks one of these rules is rejected and nothing is saved. See [errors](/guide/errors/) for the format of the response.

- The service must have a `price`.
- Price rules cannot be used on a service with `has_day_booking`.
- Two rules of the same service cannot overlap on the same weekday.
- `weekdays` must have one or more values from 1 to 7.
- `ends_at` must be after `starts_at`. A rule cannot cross midnight.
- `price` must be more than 0. When the service requires payment it must also be at least the minimum charge amount.

### Price of a booking

The price of a booking is the sum of the price of each slot, multiplied by `count`.

Example: a service has `interval` 60 and `price` 200, and a price rule with `price` 300 from `17:00` on Mondays. A booking on a Monday from 16:00 to 18:00 is two slots. The first slot starts at 16:00 and costs 200. The second starts at 17:00 and costs 300. The price is 500. With `count` 3 the price is 1500.

## Listing

`GET /services` will return all services.

`GET /services/appointments` will return all services with booking_type=appointment.

`GET /services/attendances` will return all services with booking_type=attendance.

`GET /services/products` will return all sellable services (booking_type product, gift_card, addon, subscription or pass).

`GET /services/gift_cards` will return all services with booking_type=gift_card.

`GET /services/addons` will return all services with booking_type=addon.

`GET /services/subscriptions` will return all services with booking_type=subscription.

`GET /services/passes` will return all services with booking_type=pass.

### Query Parameters

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>category_id</td><td>Integer</td><td>Only return services in this category.</td></tr>
  <tr><td>booking_type</td><td>String</td><td>Only return services with this booking type.</td></tr>
</table>

The listing includes the connected resources for each service as a nested `resources` array. Get service also includes the connected `categories`.

Response

```json
[
  {
    "service": {
      "active": true,
      "booking_capacity": 1,
      "booking_type": "appointment",
      "booking_type_id": 1,
      "description": "The best service",
      "created_at": "2012-09-20T15:34:16+02:00",
      "custom_data": null,
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

## Get service

`GET /services/{service_id}` will get a service with id `{service_id}`.

## Add new service

`POST /services` will create a new service.

## Update service

`PUT /services/{service_id}` will update existing service with id `{service_id}`.

## Get availability

`GET /services/{service_id}/availability` will return the [providers](/endpoints/providers/) for a service with id `{service_id}`. The providers define which resources provide the service and their opening hours.

Response

```json
[
  {
    "provider": {
      "created_at": "2012-09-20T15:34:16+02:00",
      "id": 1,
      "resource_id": 1,
      "service_id": 1,
      "opening_hours_mon": null,
      "opening_hours_tue": null,
      "opening_hours_wed": null,
      "opening_hours_thu": null,
      "opening_hours_fri": null,
      "opening_hours_sat": null,
      "opening_hours_sun": null,
      "priority_value": null,
      "updated_at": "2012-09-20T15:34:16+02:00"
    }
  }
]
```

## Delete service

`DELETE /services/{service_id}` will delete existing service with id `{service_id}`. Deleting a service will set it to active=false and will not be returned in any listings.
