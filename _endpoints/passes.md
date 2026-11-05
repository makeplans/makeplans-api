---
title: Passes
nav_order: 16
description: API reference for the Makeplans passes endpoint — attributes and operations for reading passes sold to customers.
---

A pass is created when a customer buys a service with booking_type=pass. Passes can only be read with the API.

## Attributes

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>id</td><td>Integer</td><td>Automatically set</td></tr>
  <tr><td>created_at</td><td>Datetime</td><td>Automatically set</td></tr>
  <tr><td>updated_at</td><td>Datetime</td><td>Automatically set</td></tr>
  <tr><td>person_id</td><td>Integer</td><td>Owner of the pass</td></tr>
  <tr><td>service_id</td><td>Integer</td><td>The service that was purchased</td></tr>
  <tr><td>quota</td><td>Integer</td><td>Number of uses. Null if unlimited</td></tr>
  <tr><td>unlimited</td><td>Boolean</td><td>Unlimited number of uses</td></tr>
  <tr><td>valid_until</td><td>Datetime</td><td>Expiry time. Null if the pass does not expire</td></tr>
  <tr><td>redeemable</td><td>Boolean</td><td>False if the pass has expired</td></tr>
  <tr><td>remaining_units</td><td>Integer</td><td>Remaining number of uses. Null if unlimited</td></tr>
</table>

## Listing

`GET /passes` will return all passes.

Response

```json
[
  {
    "pass": {
      "id": 1,
      "person_id": 888,
      "service_id": 5,
      "quota": 10,
      "unlimited": false,
      "valid_until": "2027-03-26T10:00:00+02:00",
      "created_at": "2026-10-09T10:00:00+02:00",
      "updated_at": "2026-10-09T10:00:00+02:00",
      "redeemable": true,
      "remaining_units": 8
    }
  }
]
```

### Query Parameters

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>person_id</td><td>Integer</td><td>Filter by person</td></tr>
</table>

## Get pass

`GET /passes/{pass_id}` will get a pass with id `{pass_id}`.
