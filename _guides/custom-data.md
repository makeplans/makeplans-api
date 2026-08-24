---
title: Custom Data
nav_order: 5
description: Store custom key/value data on bookings, people, services, resources, events and other objects in Makeplans.
---

Custom data is stored as key/value. All values are stored and returned as strings. Custom data can be added to booking, person, service, resource, event, category, coupon, gift card and message template. If you are using our standard booking site and would like to store custom data to a new booking please see [the custom forms documentation](https://github.com/makeplans/makeplans-custom-forms) for details on how to customise the booking form.

*Warning*: Custom data is stored as one attribute. **All** keys and values must be present when updating. If a booking has stored `custom_data` as `makeplans_is=awesome` and you want to add `signup=now` then you need to include both `makeplans_is` and `signup` with their values.
