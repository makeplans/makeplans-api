---
title: Subscriptions
nav_order: 17
description: API reference for the Makeplans subscriptions endpoint — attributes and operations for reading subscriptions sold to customers.
---

A subscription is created when a customer buys a service with booking_type=subscription. Subscriptions can only be read with the API.

## Attributes

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>id</td><td>Integer</td><td>Automatically set</td></tr>
  <tr><td>created_at</td><td>Datetime</td><td>Automatically set</td></tr>
  <tr><td>updated_at</td><td>Datetime</td><td>Automatically set</td></tr>
  <tr><td>person_id</td><td>Integer</td><td>Owner of the subscription</td></tr>
  <tr><td>service_id</td><td>Integer</td><td>The service that was purchased</td></tr>
  <tr><td>status</td><td>String</td><td>Values: active, cancelled, expired</td></tr>
  <tr><td>current_period_start</td><td>Datetime</td><td>Start of the current paid period</td></tr>
  <tr><td>current_period_end</td><td>Datetime</td><td>End of the current paid period</td></tr>
  <tr><td>cancel_at_period_end</td><td>Boolean</td><td>True if the subscription stops at the end of the current period</td></tr>
  <tr><td>quota</td><td>Integer</td><td>Number of uses per period, from the service. Null if unlimited</td></tr>
  <tr><td>unlimited</td><td>Boolean</td><td>Unlimited number of uses, from the service</td></tr>
  <tr><td>redeemable</td><td>Boolean</td><td>True if the status is active and the current period has not ended</td></tr>
  <tr><td>remaining_units</td><td>Integer</td><td>Remaining number of uses in the current period. Null if unlimited</td></tr>
</table>

## Listing

`GET /subscriptions` will return all subscriptions.

Response

```json
[
  {
    "subscription": {
      "id": 1,
      "person_id": 888,
      "service_id": 6,
      "status": "active",
      "current_period_start": "2026-10-09T10:00:00+02:00",
      "current_period_end": "2026-11-08T10:00:00+01:00",
      "cancel_at_period_end": false,
      "created_at": "2026-10-09T10:00:00+02:00",
      "updated_at": "2026-10-09T10:00:00+02:00",
      "quota": 4,
      "unlimited": false,
      "redeemable": true,
      "remaining_units": 3
    }
  }
]
```

### Query Parameters

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>person_id</td><td>Integer</td><td>Filter by person</td></tr>
</table>

## Get subscription

`GET /subscriptions/{subscription_id}` will get a subscription with id `{subscription_id}`.
