---
title: People
nav_order: 3
---

The primary key for a person is `id`. However the following fields are unique: `email`, `phone_number`, `national_id_no` and `provider`+`uid`. There are no specific requirements for input but a person needs to have either name, email, national id number or phone number.

It is possible to give a person ability to book on behalf of other people. This is linked through the `parent_id` attribute.

## Attributes

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>id</td><td>Integer</td><td>Automatically set</td></tr>
  <tr><td>created_at</td><td>Datetime</td><td>Automatically set</td></tr>
  <tr><td>updated_at</td><td>Datetime</td><td>Automatically set</td></tr>
  <tr><td>name</td><td>String</td><td>Not required.</td></tr>
  <tr><td>email</td><td>String</td><td>Not required.</td></tr>
  <tr><td>phone_number</td><td>String</td><td>Not required. Phone number as stored. Also available as phonenumber (deprecated).</td></tr>
  <tr><td>phone_number_formatted</td><td>String</td><td>Only returnable. E.164 formatted phone number with plus sign, international code, and no spaces or parentheses.</td></tr>
  <tr><td>external_id</td><td>String</td><td>Not required</td></tr>
  <tr><td>custom_data</td><td>Array</td><td>Not required. Key/value. Stored as strings.</td></tr>
  <tr><td>date_of_birth</td><td>Date</td><td>Not required</td></tr>
  <tr><td>national_id_no</td><td>String</td><td>Not required.</td></tr>
  <tr><td>street</td><td>String</td><td>Not required.</td></tr>
  <tr><td>city</td><td>String</td><td>Not required.</td></tr>
  <tr><td>postal_code</td><td>String</td><td>Not required.</td></tr>
  <tr><td>state</td><td>String</td><td>Not required.</td></tr>
  <tr><td>country_code</td><td>String</td><td>Not required. ISO 3166-1 alpha-2.</td></tr>
  <tr><td>notes</td><td>Text</td><td>Not required.</td></tr>
  <tr><td>parent_id</td><td>Integer</td><td>Id of parent.</td></tr>
</table>

## Listing

`GET /people` will return all active people.

`GET /people/all` will return all people including those who are deactivated.

Response

```json
[
  {
    "person": {
      "created_at": "2012-09-20T15:34:16+02:00",
      "custom_data": null,
      "date_of_birth": null,
      "email": "robot@booking.makeplans.com",
      "external_id": null,
      "id": 1,
      "name": "Espen Antonsen",
      "national_id_no": null,
      "notes": null,
      "phone_number": "",
      "phone_number_formatted": null,
      "date_of_birth": null,
      "street": null,
      "city": null,
      "postal_code": null,
      "state": null,
      "country_code": null,
      "parent_id": null,
      "updated_at": "2012-09-20T15:34:16+02:00"
    }
  }
]
```

### Query Parameters

You can search multiple columns at once, for example `email` and `phone_number`, or you can use the shorthand `search` to search email, phone number, national id no or name.

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>search</td><td>String</td><td></td></tr>
  <tr><td>email</td><td>String</td><td></td></tr>
  <tr><td>phone_number</td><td>String</td><td></td></tr>
  <tr><td>external_id</td><td>String</td><td></td></tr>
  <tr><td>name</td><td>String</td><td></td></tr>
  <tr><td>date_of_birth</td><td>Date</td><td></td></tr>
  <tr><td>national_id_no</td><td>String</td><td></td></tr>
  <tr><td>since</td><td>DateTime</td><td>updated_at after param</td></tr>
</table>

## Get person

`GET /people/{person_id}` will get a person with id `{person_id}`.

## Add new person

`POST /people` will create a new person.

## Update person

`PUT /people/{person_id}` will update existing person with id `{person_id}`.

## Delete person

`DELETE /people/{person_id}` will delete existing person with id `{person_id}`.

## Undelete person

`PUT /people/{person_id}/undelete` will undelete existing person with id `{person_id}`.

## Merge person with another person

`POST /people/{person_id}/merge?merge_person_id={merge_person_id}` will merge existing person with id `{person_id}` and `{merge_person_id}`. All bookings for `{merge_person_id}` will be transferred to `{person_id}`. `{merge_person_id}` will be deleted. You can also specify attributes to update with `{person}` just like when creating/updating a person.

## Person verification with a perishable token

The purpose of this feature is to verify a new or existing person in Makeplans on your website such as "login to see your bookings". The verification can be done either using email or SMS. When successful you will have verified either an email or a phone number. Thus you can either create a new person or retrieve an existing person knowing that the person has been verified.

Please note that this token is perishable, meaning that it will be removed from and unavailable for verification after 10 minutes.

### Send perishable token

`POST /people/perishable_token/send` will send a SMS with a five digit code (the token) or an email with a link which includes the token in the URL. Specify either `person[email]` or `person[phone_number]`.

*Sending via email is not yet implemented. Only SMS token is possible.*

### Verify perishable token

`POST /people/perishable_token/verify`. This will return a person if authentication is successful. If the email or phone_number is already registered to a person then that person will be returned. If no person exists with the specified email/phone_number then a empty person object will be returned. You then have to make another call to create the person. If the token and the identifier do not match an error is returned.

Specify the token as `perishable_token` and the identifier in `person[email]` or `person[phone_number]`.

## List bookings for person

`GET /people/{person_id}/bookings` will retrieve all bookings for person with id `{person_id}`. You can also use other listings as described in the [bookings listing](/endpoints/bookings/#listing) such as `GET /people/{person_id}/bookings/visible` to include cancelled bookings.

## List children for person

`GET /people/{person_id}/children` will retrieve all children for person with id `{person_id}`.
