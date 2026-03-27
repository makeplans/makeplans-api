---
title: Webhooks Endpoint
nav_order: 13
---

## Attributes

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>id</td><td>Integer</td><td>Automatically set</td></tr>
  <tr><td>created_at</td><td>Datetime</td><td>Automatically set</td></tr>
  <tr><td>updated_at</td><td>Datetime</td><td>Automatically set</td></tr>
  <tr><td>target_url</td><td>String</td><td>Required</td></tr>
  <tr><td>event</td><td>String</td><td>Required</td></tr>
  <tr><td>active</td><td>Boolean</td><td>Automatically set</td></tr>
</table>

See webhook overview for a [list of events](/guide/webhooks/#supported-events), wildcard usage to trigger from multiple events, and how to handle webhook events from Makeplans.

## Listing

`GET /web_hooks/` will return all webhooks.

Response

```json
[
  {
    "web_hook": {
      "id": 1,
      "target_url": "https://example.com/newcustomer/yeah",
      "event": "person.created",
      "active": true,
      "created_at": "2012-09-20T15:34:16+02:00",
      "updated_at": "2012-09-20T15:34:16+02:00"
    }
  }
]
```

## Get webhook

`GET /web_hook/{web_hook_id}` will get a webhook with id `{web_hook_id}`.

## Add new webhook

`POST /web_hook` will create a new webhook.

## Update webhook

`PUT /web_hook/{web_hook_id}` will update existing webhook with id `{web_hook_id}`.

## Delete webhook

`DELETE /web_hook/{web_hook_id}` will delete existing webhook with id `{web_hook_id}`.
