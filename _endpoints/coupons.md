---
title: Coupons
nav_order: 10
---

## Attributes

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>id</td><td>Integer</td><td>Automatically set</td></tr>
  <tr><td>created_at</td><td>Datetime</td><td>Automatically set</td></tr>
  <tr><td>updated_at</td><td>Datetime</td><td>Automatically set</td></tr>
  <tr><td>title</td><td>String</td><td>Required</td></tr>
  <tr><td>code</td><td>String</td><td>Required</td></tr>
  <tr><td>value_type</td><td>String</td><td>Values: percentage or amount.</td></tr>
  <tr><td>value</td><td>Integer</td><td>Required</td></tr>
  <tr><td>valid_from</td><td>Datetime</td><td></td></tr>
  <tr><td>valid_until</td><td>Datetime</td><td></td></tr>
  <tr><td>active</td><td>Boolean</td><td></td></tr>
  <tr><td>applies_to</td><td>Object</td><td></td></tr>
  <tr><td>allowed_booking_start_at</td><td>Datetime</td><td></td></tr>
  <tr><td>allowed_booking_end_at</td><td>Datetime</td><td></td></tr>
  <tr><td>min_order_amount</td><td>Decimal</td><td></td></tr>
  <tr><td>max_order_amount</td><td>Decimal</td><td></td></tr>
  <tr><td>availability</td><td>Integer</td><td>Will decrease when used</td></tr>
  <tr><td>min_count</td><td>Integer</td><td></td></tr>
  <tr><td>max_count</td><td>Integer</td><td></td></tr>
  <tr><td>custom_data</td><td>Array</td><td>Not required.</td></tr>
</table>

## Listing

`GET /coupons` will return all coupons.

Response

```json
[
  {
    "coupon": {
      "id": 1,
      "code": "SUPERWEEKND",
      "value_type": "percentage",
      "value": 50,
      "title": "Super weekend - 50% off for first 100 customers",
      "valid_from": "2024-09-11T09:30:00+02:00",
      "valid_until": null,
      "active": true,
      "applies_to": {},
      "allowed_booking_start_at": "2024-08-16T00:00:00+02:00",
      "allowed_booking_end_at": "2024-08-18T00:00:00+02:00",
      "min_order_amount": "50.0",
      "max_order_amount": "500.0",
      "availability": 100,
      "min_count": null,
      "max_count": null,
      "custom_data": null,
      "created_at": "2024-08-15T16:04:09+02:00",
      "updated_at": "2024-09-04T02:00:20+02:00"
    }
  }
]
```

## Get coupon

`GET /coupons/{coupon_id}` will get a coupon with id `{coupon_id}`.

## Add new coupon

`POST /coupons` will create a new coupon.

## Update coupon

`PUT /coupons/{coupon_id}` will update existing coupon with id `{coupon_id}`.

## Delete coupon

`DELETE /coupons/{coupon_id}` will delete existing coupon with id `{coupon_id}`.
