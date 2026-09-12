---
title: Booking site analytics
nav_order: 16
seo:
  title: Booking site analytics events
description: dataLayer and GA4 events emitted by the hosted booking site — event reference, dispatch rules and URL structure for Google Tag Manager setups.
---

The hosted booking site emits funnel events for analytics. This is the reference for developers and agencies wiring Google Tag Manager containers. For GA4 reporting guidance and a recommended GTM container setup see the help articles for [Google Tag Manager](https://help.makeplans.com/article/25-google-tag-manager) and [Google Analytics](https://help.makeplans.com/article/24-google-analytics).

Event names are a public contract and will not change.

## When events fire

Events fire only when the account has Google Tag Manager or Google Analytics configured in Makeplans under 'Setup -> Booking site'. No events fire on payment pages.

Dispatch rules:

- Availability events (`view_slots`, `view_available_dates`, `no_availability`): with GTM configured they are pushed to `window.dataLayer`; otherwise, with Google Analytics configured, they are sent directly with `gtag()`. Strictly one or the other — GTM wins.
- `booking_completed` on the booking confirmation page: pushed to `dataLayer` when GTM is configured, and sent with `gtag()` whenever Google Analytics is configured. With both integrations configured, both happen on the same page load. In that case do not also forward `booking_completed` from your GTM container to GA4, or every conversion is counted twice.
- `booking_completed` on the order confirmation page (cart checkout) follows the availability events' rule instead: dataLayer with GTM configured, otherwise `gtag()` — never both.

## Events

<table>
  <tr><th>Event</th><th>Fires when</th></tr>
  <tr><td>view_slots</td><td>Slots page rendered with at least one bookable time slot</td></tr>
  <tr><td>view_available_dates</td><td>Date-picker calendar view rendered with at least one available day</td></tr>
  <tr><td>no_availability</td><td>Either view rendered with a count of 0 (fires instead of the two above)</td></tr>
  <tr><td>booking_completed</td><td>Booking confirmation page. Also the order confirmation page after a paid cart checkout — once per booking in the order</td></tr>
  <tr><td>sign_up</td><td>A new customer record was created via the booking site (first booking or registration); never on later bookings, never for staff-created customers</td></tr>
  <tr><td>login</td><td>An existing customer authenticated (code/link or social login); not on remember-me cookie auto-login. A new registration fires sign_up only, never both</td></tr>
  <tr><td>purchase</td><td>A payment was captured. Free bookings and unpaid "pay later"/invoice bookings fire booking_completed only</td></tr>
</table>

## Payload reference

All values are strings unless noted. Multi-value fields are comma-separated strings, since GA4 does not support array parameters. An empty string on the resource fields means the visitor did not filter.

### view_slots, view_available_dates, no_availability

<table>
  <tr><th>Field</th><th>Type / example</th><th>Description</th></tr>
  <tr><td>event</td><td>"view_slots"</td><td>dataLayer only. With gtag the event name is sent as the event, not as a field.</td></tr>
  <tr><td>service_id</td><td>"1201"</td><td>Stable id, survives renames.</td></tr>
  <tr><td>service_title</td><td>"Eye exam"</td><td></td></tr>
  <tr><td>selected_resource_ids</td><td>"88" or ""</td><td>Set only when the visitor filtered on one or more resources.</td></tr>
  <tr><td>selected_resource_titles</td><td>"Oslo clinic" or ""</td><td></td></tr>
  <tr><td>slots_available</td><td>3 (number)</td><td>Time slots in the rendered range. Slots view only.</td></tr>
  <tr><td>dates_available</td><td>5 (number)</td><td>Days with availability. Calendar view only — a different unit than slots_available, hence a different parameter.</td></tr>
  <tr><td>from_date / to_date</td><td>"2026-09-14"</td><td>The rendered date range (ISO 8601). Differs on week-view templates.</td></tr>
  <tr><td>next_available_date</td><td>"2026-10-02" or null</td><td>Set only when nothing was available.</td></tr>
  <tr><td>days_to_next_available</td><td>18 (number) or null</td><td>next_available_date minus from_date.</td></tr>
</table>

`no_availability` carries whichever count field matches the view it replaced (`slots_available` on the slots view, `dates_available` on the calendar view), with the value 0.

### booking_completed

<table>
  <tr><th>Field</th><th>Type / example</th><th>Description</th></tr>
  <tr><td>booking_id</td><td>4711 (number)</td><td></td></tr>
  <tr><td>booking_title</td><td>"Eye exam"</td><td></td></tr>
  <tr><td>booking_count</td><td>1 (number)</td><td>Number of people.</td></tr>
  <tr><td>resource_id</td><td>"88"</td><td>The booked resource.</td></tr>
  <tr><td>resource_title</td><td>"Oslo clinic"</td><td></td></tr>
</table>

### sign_up and login

No parameters — the event name is the payload. No customer details are sent.

### purchase

<table>
  <tr><th>Field</th><th>Type / example</th><th>Description</th></tr>
  <tr><td>transaction_id</td><td>"booking-4711" or "order-321"</td><td>GA4's standard transaction field. Forward it unchanged — GA4 deduplicates purchases by it, covering legitimate re-fires such as revisiting the payment page of a paid booking. Dedup applies to revenue/purchase counts; raw event counts (Realtime, BigQuery) show every firing.</td></tr>
  <tr><td>value</td><td>750 (number)</td><td>The amount paid, in main currency units.</td></tr>
  <tr><td>currency</td><td>"NOK"</td><td>ISO 4217 code.</td></tr>
  <tr><td>items</td><td>array</td><td>GA4 standard e-commerce items: one entry per service line with <code>item_id</code> (service id, string), <code>item_name</code> (service title), <code>price</code> (unit price, number) and <code>quantity</code> (number). Single bookings send one item; cart checkouts send one per line item, gift cards included.</td></tr>
</table>

With GTM configured the purchase push is nested in the standard e-commerce shape — `{event: "purchase", ecommerce: {transaction_id, value, currency, items}}` — so a GA4 event tag reads it with "Send Ecommerce data" enabled (data source: Data Layer). With `gtag()` the fields are sent directly as event parameters, `items` included, which GA4 accepts as-is. The `items` array is the standard GA4 e-commerce exception to the no-arrays rule above. Payments confirmed purely by a server-to-server webhook, where the visitor's browser never returns to the site, are not emitted.

`sign_up`, `login` and `purchase` follow the availability events' dispatch rule: dataLayer with GTM configured, otherwise `gtag()` — never both. As GA4 standard event names they need no renaming in a GTM container.

## URL structure

The booking site URL structure identifies most funnel steps on its own, usable with plain GTM page-path triggers:

- Service list: `/services` — and `/categories/<id>-<slug>/services` for accounts using categories. Cover both.
- Slots: `/services/<id>-<slug>/slots?selected_resources=<id>&from=<date>`
- The service id can be extracted from the path with the regex `^/services/(\d+)`. Never match on the slug — it changes when the service is renamed.

## Custom JavaScript

You can add your own JavaScript to the booking site footer with the {% raw %}`{% script %}`{% endraw %} mechanism under 'Setup -> Booking site'. If you previously pushed a custom event inside the no availability text as a workaround, remove it — otherwise `no_availability` is emitted twice.
