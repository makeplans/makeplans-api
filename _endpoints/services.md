---
title: Services
nav_order: 4
---

There are four types of services:

* Appointment
* Attendance
* Product
* Gift card

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
  <tr><td>day_booking_specify_time</td><td>Boolean</td><td>Not required (default false)</td></tr>
  <tr><td>has_day_booking</td><td>Boolean</td><td>Not required (default false)</td></tr>
  <tr><td>interval</td><td>Integer</td><td>Not required (default 60)</td></tr>
  <tr><td>max_slots</td><td>Integer</td><td>Not required (default 1)</td></tr>
  <tr><td>price</td><td>Decimal</td><td>Not required.</td></tr>
  <tr><td>same_day</td><td>Boolean</td><td>Not required (default false)</td></tr>
  <tr><td>template</td><td>String</td><td>Component template (calendar view)</td></tr>
  <tr><td>interval_rounding</td><td>Integer</td><td>Overrides account default (see info on account object)</td></tr>
  <tr><td>booking_type</td><td>String</td><td>Required. Values: appointment (default), attendance, product, gift_card</td></tr>
  <tr><td>custom_data</td><td>Array</td><td>Not required</td></tr>
  <tr><td>booking_form</td><td>Liquid-Text</td><td>Custom booking form</td></tr>
  <tr><td>mail_verification</td><td>Liquid-Text</td><td></td></tr>
  <tr><td>mail_confirmation</td><td>Liquid-Text</td><td></td></tr>
  <tr><td>sms_verification</td><td>Liquid-Text</td><td></td></tr>
  <tr><td>sms_confirmation</td><td>Liquid-Text</td><td></td></tr>
  <tr><td>sms_reminder</td><td>Liquid-Text</td><td></td></tr>
  <tr><td>mail_modification</td><td>Liquid-Text</td><td></td></tr>
  <tr><td>mail_awaiting_confirmation</td><td>Liquid-Text</td><td></td></tr>
  <tr><td>mail_cancellation</td><td>Liquid-Text</td><td></td></tr>
  <tr><td>sms_modification</td><td>Liquid-Text</td><td></td></tr>
  <tr><td>sms_awaiting_confirmation</td><td>Liquid-Text</td><td></td></tr>
  <tr><td>sms_cancellation</td><td>Liquid-Text</td><td></td></tr>
  <tr><td>next_interval</td><td>Integer</td><td>Not required</td></tr>
  <tr><td>payment_required</td><td>Boolean</td><td>Not required</td></tr>
  <tr><td>allow_invoice</td><td>Boolean</td><td>Not required</td></tr>
  <tr><td>booking_minimum</td><td>Integer</td><td>Not required</td></tr>
  <tr><td>terms</td><td>Text</td><td>Not required</td></tr>
  <tr><td>priority_strategy</td><td>String</td><td>Not required. Strategy for resource priority assignment.</td></tr>
  <tr><td>availability_type</td><td>String</td><td>Not required. Type of availability calculation.</td></tr>
  <tr><td>priority_value</td><td>Integer</td><td>Not required. Priority value for ordering.</td></tr>
</table>

### Deprecated attributes

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>booking_type_id</td><td>Integer</td><td>1: appointment. 2: attendance.</td></tr>
</table>

## Listing

`GET /services` will return all services.

`GET /services/appointments` will return all services with booking_type=appointment.

`GET /services/attendances` will return all services with booking_type=attendance.

`GET /services/products` will return all services with booking_type=product.

`GET /services/gift_cards` will return all services with booking_type=gift_card.

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

`GET /services/{service_id}/availability` will return available resources for a service with id `{service_id}`.

## Delete service

`DELETE /services/{service_id}` will delete existing service with id `{service_id}`. Deleting a service will set it to active=false and will not be returned in any listings.
