---
title: Account
nav_order: 14
---

Information and settings for your account.

## Attributes

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>id</td><td>Integer</td><td>Automatically set</td></tr>
  <tr><td>created_at</td><td>Datetime</td><td>Automatically set</td></tr>
  <tr><td>updated_at</td><td>Datetime</td><td>Automatically set</td></tr>
  <tr><td>subdomain</td><td>String</td><td>Required</td></tr>
  <tr><td>email</td><td>String</td><td>Required</td></tr>
  <tr><td>name</td><td>String</td><td>Required</td></tr>
  <tr><td>google_analytics</td><td>String</td><td></td></tr>
  <tr><td>css_url</td><td>String</td><td></td></tr>
  <tr><td>time_zone</td><td>String</td><td>Required</td></tr>
  <tr><td>verification_method</td><td>Integer</td><td></td></tr>
  <tr><td>address</td><td>String</td><td></td></tr>
  <tr><td>country_code</td><td>String</td><td>Required. ISO 3166-1 alpha-2.</td></tr>
  <tr><td>google_merchant_id</td><td>String</td><td></td></tr>
  <tr><td>google_merchant_key</td><td>String</td><td></td></tr>
  <tr><td>logo</td><td>File</td><td></td></tr>
  <tr><td>template</td><td>Integer</td><td>Required</td></tr>
  <tr><td>service_template</td><td>String</td><td></td></tr>
  <tr><td>locale</td><td>String</td><td>Required. Supported locales: 'en', 'sv' and 'nb'. Default: 'en'.</td></tr>
  <tr><td>currency</td><td>String</td><td>ISO4217 currency code</td></tr>
  <tr><td>email_notification</td><td>Boolean</td><td></td></tr>
  <tr><td>mail_notification_recipient</td><td>Email</td><td></td></tr>
  <tr><td>sms_notification</td><td>Boolean</td><td></td></tr>
  <tr><td>phone_number</td><td>String</td><td></td></tr>
  <tr><td>confirm_manually</td><td>Boolean</td><td></td></tr>
  <tr><td>settings</td><td>Array</td><td></td></tr>
  <tr><td>public_access</td><td>Boolean</td><td></td></tr>
  <tr><td>interval_rounding</td><td>Integer</td><td>Default setting for all services for rounding to next available time. '15' will round 10:07 to become 10:15 as next available slot time.</td></tr>
  <tr><td>slot_generation_type</td><td>Integer</td><td>Default: 2. 1=fixed. 2=next available.</td></tr>
  <tr><td>future_bookable_period</td><td>String</td><td>Natural language date/time relative to current time.</td></tr>
  <tr><td>first_bookable_period</td><td>String</td><td>Natural language date/time relative to current time.</td></tr>
  <tr><td>payment_provider</td><td>String</td><td></td></tr>
  <tr><td>footer</td><td>Text</td><td></td></tr>
  <tr><td>verification_methods</td><td>String</td><td></td></tr>
  <tr><td>reminder_sms</td><td>Boolean</td><td></td></tr>
  <tr><td>closed_for_holidays</td><td>Boolean</td><td></td></tr>
  <tr><td>new_user_text</td><td>Liquid-Text</td><td>Above booking form.</td></tr>
  <tr><td>new_booking_text</td><td>Liquid-Text</td><td>Booking confirmation page.</td></tr>
  <tr><td>booking_form</td><td>Liquid-Text</td><td>Custom booking form.</td></tr>
  <tr><td>person_form</td><td>Liquid-Text</td><td>For custom data in administration system.</td></tr>
  <tr><td>resource_form</td><td>Liquid-Text</td><td>For custom data in administration system.</td></tr>
  <tr><td>service_form</td><td>Liquid-Text</td><td>For custom data in administration system.</td></tr>
  <tr><td>event_form</td><td>Liquid-Text</td><td>For custom data in administration system.</td></tr>
  <tr><td>appointment_form</td><td>Liquid-Text</td><td>For custom data in administration system.</td></tr>
  <tr><td>category_form</td><td>Liquid-Text</td><td>For custom data in administration system.</td></tr>
  <tr><td>sms_verification</td><td>Liquid-Text</td><td></td></tr>
  <tr><td>sms_confirmation</td><td>Liquid-Text</td><td></td></tr>
  <tr><td>sms_modification</td><td>Liquid-Text</td><td></td></tr>
  <tr><td>sms_cancellation</td><td>Liquid-Text</td><td></td></tr>
  <tr><td>sms_reminder</td><td>Liquid-Text</td><td></td></tr>
  <tr><td>mail_verification</td><td>Liquid-Text</td><td></td></tr>
  <tr><td>mail_confirmation</td><td>Liquid-Text</td><td></td></tr>
  <tr><td>mail_modification</td><td>Liquid-Text</td><td></td></tr>
  <tr><td>mail_cancellation</td><td>Liquid-Text</td><td></td></tr>
  <tr><td>mail_verification_subject</td><td>String</td><td></td></tr>
  <tr><td>mail_confirmation_subject</td><td>String</td><td></td></tr>
  <tr><td>mail_modification_subject</td><td>String</td><td></td></tr>
  <tr><td>mail_cancellation_subject</td><td>String</td><td></td></tr>
  <tr><td>allow_cancellation</td><td>Boolean</td><td></td></tr>
  <tr><td>cancellation_period</td><td>String</td><td>Natural language date/time relative to start of booking.</td></tr>
</table>

### Additional parameters

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>remove_logo</td><td>Boolean</td><td></td></tr>
</table>

## Get account

`GET /client` will get the account.

## Update account

`PUT /client` will update the account.

## Holidays

Who doesn't like a holiday?

### Listing

`GET /client/holidays` will get holidays for account country.

Response

```json
[
  {
    "holiday": {
      "date": "2016-01-01",
      "name": "New Year's Day"
    }
  },
  {
    "holiday": {
      "date": "2016-05-01",
      "name": "International Workers' Day"
    }
  }
]
```

#### Query Parameters

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>from</td><td>Date</td><td></td></tr>
  <tr><td>to</td><td>Date</td><td></td></tr>
</table>

## Users

Users who can login into the account.

### Attributes for user role on the account

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>id</td><td>Integer</td><td>Automatically set</td></tr>
  <tr><td>created_at</td><td>Datetime</td><td>Automatically set</td></tr>
  <tr><td>updated_at</td><td>Datetime</td><td>Automatically set</td></tr>
  <tr><td>user_id</td><td>Integer</td><td>Automatically set</td></tr>
  <tr><td>role</td><td>String</td><td>admin, manager or staff</td></tr>
  <tr><td>user</td><td>Object</td><td>See attributes for a user</td></tr>
</table>

### Attributes for user

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>id</td><td>Integer</td><td>Automatically set</td></tr>
  <tr><td>created_at</td><td>Datetime</td><td>Automatically set</td></tr>
  <tr><td>updated_at</td><td>Datetime</td><td>Automatically set</td></tr>
  <tr><td>name</td><td>String</td><td>Required</td></tr>
  <tr><td>email</td><td>String</td><td>Required</td></tr>
  <tr><td>phone_number</td><td>String</td><td>Also available as phonenumber (deprecated).</td></tr>
</table>

### Listing

`GET /client/users` will list all users with access to the account.

Response

```json
[
  {
    "client_user_link": {
      "id": 1337,
      "created_at": "2016-11-07T07:26:32+01:00",
      "updated_at": "2016-11-07T07:27:42+01:00",
      "role": "admin",
      "user_id": 1,
      "user": {
          "id": 1,
          "email": "maestro@example.org",
          "phone_number": "180",
          "name": "Espen Antonsen",
          "created_at": "2016-11-07T07:26:32+01:00",
          "updated_at": "2016-11-07T07:27:42+01:00"
      }
    }
  }
]
```

## Get user

`GET /client/users/{user_link_id}` will get a user with id `{user_link_id}`.

### Add new user

`GET /client/users` will create a new user. An email is sent to the user so the user can specify their password.
