---
title: Gift Cards
nav_order: 12
---

## Attributes

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>id</td><td>Integer</td><td>Automatically set</td></tr>
  <tr><td>created_at</td><td>Datetime</td><td>Automatically set</td></tr>
  <tr><td>updated_at</td><td>Datetime</td><td>Automatically set</td></tr>
  <tr><td>code</td><td>String</td><td>Unique per account. Max 50 characters. Automatically generated if not specified. Format: XXXX-9999-XXXX</td></tr>
  <tr><td>value</td><td>Integer</td><td>Required. Current remaining value. Must be greater than 0 and less than or equal to 10000</td></tr>
  <tr><td>initial_value</td><td>Integer</td><td>Original value when created</td></tr>
  <tr><td>active</td><td>Boolean</td><td>Default: true</td></tr>
  <tr><td>valid_until</td><td>Date</td><td>Expiry date. Gift card is not redeemable after this date</td></tr>
  <tr><td>person_id</td><td>Integer</td><td>Owner of the gift card</td></tr>
  <tr><td>purchased_by_person_id</td><td>Integer</td><td>Person who purchased the gift card</td></tr>
  <tr><td>custom_data</td><td>Object</td><td>Key/value. Stored as strings</td></tr>
</table>

## Listing

`GET /gift_cards` will return all active gift cards.

Response

```json
[
  {
    "gift_card": {
      "id": 1,
      "code": "ABCD-2345-EFGH",
      "initial_value": 500,
      "value": 500,
      "valid_until": null,
      "active": true,
      "custom_data": null,
      "created_at": "2026-03-26T10:00:00+02:00",
      "updated_at": "2026-03-26T10:00:00+02:00"
    }
  }
]
```

### Query Parameters

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>person_id</td><td>Integer</td><td>Filter by person</td></tr>
</table>

## Get gift card

`GET /gift_cards/{gift_card_id}` will get a gift card with id `{gift_card_id}`.

## Add new gift card

`POST /gift_cards` will create a new gift card.

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>code</td><td>String</td><td>Not required. Auto-generated if not specified</td></tr>
  <tr><td>value</td><td>Integer</td><td>Required</td></tr>
  <tr><td>initial_value</td><td>Integer</td><td>Not required</td></tr>
  <tr><td>active</td><td>Boolean</td><td>Not required</td></tr>
  <tr><td>valid_until</td><td>Date</td><td>Not required</td></tr>
  <tr><td>person_id</td><td>Integer</td><td>Not required</td></tr>
  <tr><td>custom_data</td><td>Object</td><td>Not required</td></tr>
</table>

## Update gift card

`PUT /gift_cards/{gift_card_id}` will update existing gift card with id `{gift_card_id}`.

## Delete gift card

`DELETE /gift_cards/{gift_card_id}` will delete existing gift card with id `{gift_card_id}`.
