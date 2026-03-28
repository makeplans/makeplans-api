---
title: Synchronisation
nav_order: 11
---

Synchronising data is hard. Please ensure you test before releasing to production. First pick either Makeplans or the other system as a master. If the other system is chosen as a master then we recommend enabling the 'confirmation by administrator' setting for bookings. The synchronisation should then retrieve unprocessed bookings and process them (confirm/decline). This will ensure you can handle any changes occurred in the other system since the last synchronisation with Makeplans. New unprocessed bookings must be processed often (every 1-5 minutes) to ensure confirmations are sent out quickly to the end-user after requesting a new reservation.

**NEVER** delete any data in Makeplans to make it easier to adapt to the other system. Makeplans is a customer facing booking application. End-users (stored as people) can change and cancel bookings, thus any modifications or destruction of core data should not occur. Bookings are stored with a history (log) and there is a link between a person and bookings. Makeplans make use of this data and all this data must be kept to ensure the booking process in Makeplans works as expected for the end-user.

Expect all booking and person data to be changed at any time. All changes for an object will result in an updated attribute `updated_at`.

We recommend storing a timestamp retrieved from the most recently updated item from `updated_at`. When the synchronisation is performed again this timestamp can be used to fetch any changes on the `updated_at` attribute for the object you want to retrieve (e.g. the parameter `since` for bookings).
