# MakePlans API Changelog

## 2017-08-10

**Maximum limit for recurring occurrences**

Previously there was a limit of 366 occurences when creating recurring bookings or events. When going above this limit no detailed error message was returned. We now return a detailed error message on the `collection_id` attribute. In addition we have increased the maximum limit of occurences to 731.

## 2017-07-05

**Verification method attribute requirement for existing person**

*BREAKING CHANGE*

When verification is required the attribute of the verification method is required to complete a booking with `confirm=false`. So if SMS verification is used then `phone_number` is a required field for `person_attributes`. This now also applies when specifying a `person_id`, so a person without a phone number will not be able to intiate the verification process.

In general it is advisable to only specify `person_id` when you have authenticated this person. In all other cases you should supply `person_attributes` so MakePlans can match and verify the person using the supplied values.

Endpoint affected: POST `/bookings/?confirm=false`

**Valid phone number required when veryifing a booking**

*BREAKING CHANGE*

When SMS verification is used the supplied `phone_number` (either using `person_attributes` or the value from an existing person) is now validated to ensure it is a proper phone number. Currently any phone number can be specified but in the future only mobile phone numbers will be allowed since an SMS will be sent to the phone number.

Endpoint affected: POST `/bookings/?confirm=false`

**Public booking will not check for verification method attributes**

*BREAKING CHANGE*

Previously specifying `public_booking=true` would validate required verification method attribute (such as `phone_number`). From now on this validation is only performed when verification is requested by specyfing `confirm=false`.

Endpoint affected: POST `/bookings/?public_booking=true`

**Removed `base` error for person errors when creating new booking**

*BREAKING CHANGE*

`base` is no longer returned when there is an error with the person supplied with a new booking. Instead the `person` attribute now contains information about errors related to the person. In addition detailed errors are returned in `person_attributes`.

Endpoint affected: POST `/bookings/`

Previously:
```json
{
  "base": [
    "Person errors"
  ]
}
```

Now:
```json
{
  "person": [
    "is invalid"
  ],
  "person_attributes": {
    "email": [
      "cannot be empty"
    ]
  }
}
```
