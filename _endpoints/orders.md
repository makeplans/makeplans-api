---
title: Orders
nav_order: 11
---

## Attributes

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>id</td><td>Integer</td><td>Automatically set</td></tr>
  <tr><td>created_at</td><td>Datetime</td><td>Automatically set</td></tr>
  <tr><td>updated_at</td><td>Datetime</td><td>Automatically set</td></tr>
  <tr><td>person_id</td><td>Integer</td><td></td></tr>
  <tr><td>state</td><td>String</td><td>pending, confirmed or cancelled</td></tr>
  <tr><td>total</td><td>Decimal</td><td>Total amount before discount</td></tr>
  <tr><td>discount_amount</td><td>Decimal</td><td>Discount from applied coupon</td></tr>
  <tr><td>coupon_id</td><td>Integer</td><td>Applied coupon</td></tr>
  <tr><td>paid_amount</td><td>Decimal</td><td>Total amount paid</td></tr>
  <tr><td>settled_at</td><td>Datetime</td><td>Set when order is fully paid</td></tr>
  <tr><td>balance</td><td>Decimal</td><td>Calculated. payable_amount - paid_amount</td></tr>
  <tr><td>payable_amount</td><td>Decimal</td><td>Calculated. total - discount_amount</td></tr>
</table>

The response includes nested `person` and `order_line_items`.

### Person

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>id</td><td>Integer</td><td></td></tr>
  <tr><td>name</td><td>String</td><td></td></tr>
  <tr><td>email</td><td>String</td><td></td></tr>
</table>

### Order line items

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>id</td><td>Integer</td><td></td></tr>
  <tr><td>service_id</td><td>Integer</td><td></td></tr>
  <tr><td>quantity</td><td>Integer</td><td></td></tr>
  <tr><td>amount</td><td>Decimal</td><td>Unit price</td></tr>
  <tr><td>gift_card_id</td><td>Integer</td><td>Associated gift card if applicable</td></tr>
  <tr><td>booking_id</td><td>Integer</td><td>Associated booking if applicable</td></tr>
</table>

## Listing

`GET /orders` will return all orders. Orders are read-only via the API.

Response

```json
[
  {
    "order": {
      "id": 1,
      "person_id": 38,
      "state": "confirmed",
      "total": "1000.0",
      "discount_amount": "0.0",
      "coupon_id": null,
      "paid_amount": "0.0",
      "settled_at": null,
      "created_at": "2026-03-26T10:00:00+02:00",
      "updated_at": "2026-03-26T10:00:00+02:00",
      "balance": "1000.0",
      "payable_amount": "1000.0",
      "person": {
        "id": 38,
        "name": "Espen Antonsen",
        "email": "espen@example.com"
      },
      "order_line_items": [
        {
          "id": 1,
          "service_id": 37,
          "quantity": 1,
          "amount": "1000.0",
          "gift_card_id": null,
          "booking_id": null
        }
      ]
    }
  }
]
```

### Query Parameters

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>state</td><td>String</td><td>Filter by state: pending, confirmed or cancelled</td></tr>
  <tr><td>person_id</td><td>Integer</td><td>Filter by person</td></tr>
</table>

## Get order

`GET /orders/{order_id}` will get an order with id `{order_id}`.
