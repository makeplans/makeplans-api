---
title: Room display
nav_order: 17
seo:
  title: Custom room display
description: Build a custom room display with the Makeplans room display endpoint — pairing code setup and the events feed for a single resource.
---

Makeplans has a built-in room display which shows today's schedule for a single resource, meant for a screen mounted outside a meeting room or at a treatment room. It is available at `https://youraccount.makeplans.com/room_display/{resource_id}` after enabling it on the resource.

You can also build your own display. The display device authenticates with a one-time pairing code and does not need your API-Key, so it is safe to use on an unattended device. The credential gives read-only access to the schedule of one resource only.

## Enabling

In the administration system go to the resource page and enable the room display. Then generate a pairing code on the same page. The pairing code is 6 digits and is valid for 10 minutes. You can generate a new code at any time, and disabling the room display invalidates all paired displays.

## Pairing

Your display exchanges the pairing code for a long-lived cookie:

1. `GET https://youraccount.makeplans.com/room_display/{resource_id}/setup`. Store the cookies from the response and extract the value of the hidden `authenticity_token` input from the form (CSRF protection).
2. `POST https://youraccount.makeplans.com/room_display/{resource_id}/verify_token` as a form post with the fields `token` (the pairing code) and `authenticity_token`, sending the cookies from step 1.
3. A valid code returns `302 Found` and sets the cookie `room_display_token_{resource_id}`. Store this cookie — it is the display credential and is valid for 365 days. An invalid or expired code returns `200 OK` with the setup form again.

The cookie value is a signed token. Treat it as an opaque string and send it back unmodified.

## Get events

<pre>GET https://youraccount.makeplans.com/api/v1/bookings/room_display/events?resource_id={resource_id}</pre>

Send the stored `room_display_token_{resource_id}` cookie with the request. No other authentication is required. A missing or invalid credential, or an unknown resource, returns `404 Not Found` — send the display back to pairing when this happens.

The response is a JSON array of bookings and events for the resource, sorted by start time:

* Active (confirmed and tentative) appointment [bookings](/endpoints/bookings/) with `booked_to` from the start of the current day in the account's time zone. Each booking includes the related `person`, `service` and `resource`.
* Active [events](/endpoints/events/) with `ends_at` from the start of the current day. Each event includes the related `service` and `resource`.

Each item is wrapped in its resource name, so check for the `booking` or `event` key to tell them apart:

```json
[
  {
    "booking": {
      "id": 100,
      "booked_from": "2026-09-29T10:00:00+02:00",
      "booked_to": "2026-09-29T11:00:00+02:00",
      "state": "confirmed",
      "title": null,
      "person": { "name": "Anna Larsen" },
      "service": { "id": 5, "title": "Meeting" },
      "resource": { "id": 1, "title": "Room 1" }
    }
  },
  {
    "event": {
      "id": 200,
      "title": "All hands",
      "starts_at": "2026-09-29T13:00:00+02:00",
      "ends_at": "2026-09-29T14:00:00+02:00",
      "service": { "id": 6, "title": "Company events" },
      "resource": { "id": 1, "title": "Room 1" }
    }
  }
]
```

The response contains a reduced set of the attributes documented for the [bookings](/endpoints/bookings/) and [events](/endpoints/events/) endpoints:

* Booking: `id`, `booked_from`, `booked_to`, `service_id`, `resource_id`, `person_id`, `expires_at`, `title`, `notes`, `state`, `count`, `booking_type`, with nested `person` (`id`, `name`), `service` (`id`, `title`) and `resource` (`id`, `title`).
* Event: `id`, `title`, `service_id`, `resource_id`, `starts_at`, `ends_at`, `capacity`, `nr_of_attendances`, with nested `service` and `resource` (`id`, `title`).

See [date handling](/guide/date-handling/) for datetime formats.

There is no pagination. The response is limited to 750 bookings and 750 events.

## Building the display

* Poll the events endpoint every 5 minutes. The built-in display uses this interval.
* There are no time filter parameters. The feed always starts at the beginning of the current day, so filter on the device if you only want to show today.
* The endpoint does not send CORS headers, so it can not be called with JavaScript from a page on another domain. Call it from a native or kiosk app, or proxy it through your own server.
* The response includes the customer name for each booking. Only show what is appropriate on a public screen.
