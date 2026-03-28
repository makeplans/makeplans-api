---
title: Message Templates
nav_order: 13
---

Message templates define the content of messages sent to customers for booking events such as confirmation, reminder, and follow-up.

## Attributes

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>id</td><td>Integer</td><td>Automatically set</td></tr>
  <tr><td>created_at</td><td>Datetime</td><td>Automatically set</td></tr>
  <tr><td>updated_at</td><td>Datetime</td><td>Automatically set</td></tr>
  <tr><td>message_type</td><td>String</td><td>Values: confirmation, reminder, follow_up. See message types.</td></tr>
  <tr><td>delivery_type</td><td>String</td><td>Values: email, sms.</td></tr>
  <tr><td>subject</td><td>String</td><td>Message subject line. Only applicable for email.</td></tr>
  <tr><td>template</td><td>Liquid-Text</td><td>Message body template.</td></tr>
  <tr><td>delivery_timing</td><td>String</td><td>When the message should be delivered. See delivery timing.</td></tr>
  <tr><td>active</td><td>Boolean</td><td>Whether the template is active.</td></tr>
  <tr><td>services</td><td>Array</td><td>Service IDs this template applies to.</td></tr>
  <tr><td>custom_data</td><td>Array</td><td>Not required</td></tr>
</table>

## Message types

* `confirmation` — sent when a booking is created.
* `reminder` — sent before a booking starts.
* `follow_up` — sent after a booking ends.

## Delivery timing

The `delivery_timing` value is a natural language time expression parsed relative to a base time that depends on `message_type`:

<table>
  <tr><th>message_type</th><th>Base time</th><th>Example delivery_timing</th><th>Result</th></tr>
  <tr><td>confirmation</td><td>created_at (booking creation)</td><td>in 5 minutes</td><td>5 min after booking is created</td></tr>
  <tr><td>reminder</td><td>starts_at (booking start)</td><td>3 hours ago</td><td>3 hours before booking starts</td></tr>
  <tr><td>reminder</td><td>starts_at</td><td>1 day ago</td><td>1 day before booking starts</td></tr>
  <tr><td>follow_up</td><td>ends_at (booking end)</td><td>in 2 hours</td><td>2 hours after booking ends</td></tr>
  <tr><td>follow_up</td><td>ends_at</td><td>in 1 day</td><td>1 day after booking ends</td></tr>
</table>

The expression is parsed relative to the base time. So `3 hours ago` relative to `starts_at` means 3 hours *before* the booking starts, and `in 2 hours` relative to `ends_at` means 2 hours *after* the booking ends.

## Listing

`GET /message_templates` will return all message templates.

Response

```json
[
  {
    "message_template": {
      "id": 1,
      "created_at": "2012-09-20T15:34:16+02:00",
      "updated_at": "2012-09-20T15:34:16+02:00",
      "message_type": "confirmation",
      "delivery_type": "email",
      "subject": "Your booking is confirmed",
      "template": "Hi {{ person.name }}, your booking is confirmed.",
      "delivery_timing": "in 5 minutes",
      "active": true,
      "services": [1, 2],
      "custom_data": null
    }
  }
]
```

## Get message template

`GET /message_templates/{message_template_id}` will get a message template with id `{message_template_id}`.

## Add new message template

`POST /message_templates` will create a new message template.

## Update message template

`PUT /message_templates/{message_template_id}` will update existing message template with id `{message_template_id}`.

## Delete message template

`DELETE /message_templates/{message_template_id}` will delete existing message template with id `{message_template_id}`.
