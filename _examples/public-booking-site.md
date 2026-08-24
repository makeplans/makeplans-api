---
title: "Public Booking Site"
nav_order: 2
description: Examples of building a public booking site with the Makeplans public API using JavaScript.
---

Please familiarise yourself with the [key concepts of the Makeplans API](/guide/getting-started/) first.

The public API requires no authentication so you can call it directly from the browser with JavaScript. The browser sets the `User-Agent` header automatically so you only need to ask for JSON with the `Accept` header.

Please note: cross-origin requests from the browser are only allowed from the website configured as the embed URL on the account. Without it the browser will block the requests with a CORS error.

## List available services

First we want to show a list of available services on the booking site.

<details markdown="1">
<summary>Request</summary>

```javascript
const response = await fetch('https://youraccount.test.makeplans.net/services', {
  headers: { 'Accept': 'application/json' }
});
const services = await response.json();
```
</details>

<details markdown="1">
<summary>Response</summary>

```http
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

```json
[
  {
    "service": {
      "id": 16,
      "title": "Body scrub",
      "created_at": "2020-01-24T06:24:16+01:00",
      "updated_at": "2020-01-24T06:25:15+01:00",
      "interval": 45,
      "booking_capacity": 1,
      "max_slots": 1,
      "same_day": false,
      "price": null,
      "booking_minimum": null,
      "booking_type": "appointment",
      "booking_type_id": 1,
      "custom_data": null,
      "linked_service_ids": []
    }
  },
  {
    "service": {
      "id": 15,
      "title": "Massage",
      "created_at": "2020-01-24T06:24:16+01:00",
      "updated_at": "2020-01-24T06:25:15+01:00",
      "interval": 60,
      "booking_capacity": 1,
      "max_slots": 1,
      "same_day": false,
      "price": null,
      "booking_minimum": null,
      "booking_type": "appointment",
      "booking_type_id": 1,
      "custom_data": null,
      "linked_service_ids": []
    }
  }
]
```
</details>

### Displaying the services

Each item in the response is wrapped in a `service` key. Loop over the response and render each service using an HTML `<template>`:

```html
<ul id="services"></ul>

<template id="service-template">
  <li>
    <button type="button" class="service-title"></button>
  </li>
</template>
```

```javascript
async function loadServices() {
  const response = await fetch('https://youraccount.test.makeplans.net/services', {
    headers: { 'Accept': 'application/json' }
  });
  const services = await response.json();

  const list = document.querySelector('#services');
  const template = document.querySelector('#service-template');

  for (const { service } of services) {
    const item = template.content.cloneNode(true);
    const button = item.querySelector('.service-title');
    button.textContent = service.title;
    button.addEventListener('click', () => loadSlots(service.id));
    list.appendChild(item);
  }
}

loadServices();
```

## Find available timeslots for a service

The user selects 'Massage' so let's get all available timeslots for 'Massage' (service_id 15).

By default this will return timeslots for today, but you can specify the timeframe using from/to parameters.

<details markdown="1">
<summary>Request</summary>

```javascript
const response = await fetch('https://youraccount.test.makeplans.net/services/15/slots', {
  headers: { 'Accept': 'application/json' }
});
const slots = await response.json();
```
</details>

<details markdown="1">
<summary>Response</summary>

```http
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

```json
[
  {
    "timestamp": "2020-01-24T09:00:00+01:00",
    "timestamp_end": "2020-01-24T10:00:00+01:00",
    "formatted_timestamp": "Friday, 24 January 2020, 09:00",
    "formatted_timestamp_end": "Friday, 24 January 2020, 10:00",
    "free": 3,
    "available_resources": [
      10,
      11,
      12
    ],
    "maximum_capacity": 3
  },
  {
    "timestamp": "2020-01-24T10:00:00+01:00",
    "timestamp_end": "2020-01-24T11:00:00+01:00",
    "formatted_timestamp": "Friday, 24 January 2020, 10:00",
    "formatted_timestamp_end": "Friday, 24 January 2020, 11:00",
    "free": 3,
    "available_resources": [
      10,
      11,
      12
    ],
    "maximum_capacity": 3
  }
]
```
</details>

### Displaying the timeslots

Unlike the services list the public slots response is a plain array without a wrapping key, and `formatted_timestamp` gives you a ready-to-display time:

```html
<ul id="slots"></ul>

<template id="slot-template">
  <li>
    <button type="button" class="slot-time"></button>
  </li>
</template>
```

```javascript
async function loadSlots(serviceId) {
  const response = await fetch(`https://youraccount.test.makeplans.net/services/${serviceId}/slots`, {
    headers: { 'Accept': 'application/json' }
  });
  const slots = await response.json();

  const list = document.querySelector('#slots');
  const template = document.querySelector('#slot-template');

  list.replaceChildren();
  for (const slot of slots) {
    const item = template.content.cloneNode(true);
    item.querySelector('.slot-time').textContent = slot.formatted_timestamp;
    list.appendChild(item);
  }
}
```

## Putting it all together

The two examples above combine into a complete page: the services are listed on load, and selecting a service shows its available timeslots.

```html
<h2>Services</h2>
<ul id="services"></ul>

<h2>Available times</h2>
<ul id="slots"></ul>

<template id="service-template">
  <li>
    <button type="button" class="service-title"></button>
  </li>
</template>

<template id="slot-template">
  <li>
    <button type="button" class="slot-time"></button>
  </li>
</template>

<script>
const baseUrl = 'https://youraccount.test.makeplans.net';

async function fetchJson(path) {
  const response = await fetch(baseUrl + path, {
    headers: { 'Accept': 'application/json' }
  });
  return response.json();
}

async function loadServices() {
  const services = await fetchJson('/services');

  const list = document.querySelector('#services');
  const template = document.querySelector('#service-template');

  for (const { service } of services) {
    const item = template.content.cloneNode(true);
    const button = item.querySelector('.service-title');
    button.textContent = service.title;
    button.addEventListener('click', () => loadSlots(service.id));
    list.appendChild(item);
  }
}

async function loadSlots(serviceId) {
  const slots = await fetchJson(`/services/${serviceId}/slots`);

  const list = document.querySelector('#slots');
  const template = document.querySelector('#slot-template');

  list.replaceChildren();
  for (const slot of slots) {
    const item = template.content.cloneNode(true);
    item.querySelector('.slot-time').textContent = slot.formatted_timestamp;
    list.appendChild(item);
  }
}

loadServices();
</script>
```
