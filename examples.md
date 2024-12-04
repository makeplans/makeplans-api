# Examples

There are two ways of using the MakePlans API:

## Public API (For direct communication on website with JavaScript)

If you are implementing a custom booking experience on your website using JavaScript we have a public API available. It requires no authentication and allows you to replicate functionality from our standard booking site. So you can list services, find available timeslots and initiate the booking process.

[See public API examples.](examples_public_booking_site.md)

## Private API (For backend integration)

To configure your account using the API you need to authenticate with our private API. It will give you full control over your account just as the administration system. So you can create new services, define opening hours and fully manage all appointments as well as customers. You can also use this create a custom booking experience on your website as long as the API communication from your server. In that case there are ways of limiting availability and bookings in the same was as the public booking site. If you do not specify this limitation your API calls will have full access to the calendar, which is useful when creating an integration that will synchronize your internal application with MakePlans.

[See private API examples.](examples_private.md)

## Webhooks

[See webook examples.](examples_webhook.md)