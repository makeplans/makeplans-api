# Partner API Documentation

The partner account creation URL is `https://app.makeplans.com` for production apps and `https://app.test.makeplans.net` for test apps.

Only account creation is done at this URL. All other requests are done on the URL for the API account subdomain.

## Account

### Add new account

`POST /client` will create a new client and user.

The initial user is set as admin on the account. The attributes for creation are limited. See API documentation for updating other attributes on the account or to add additional users.

#### Client Attributes

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>subdomain</td><td>String</td><td>Required</td></tr>
  <tr><td>email</td><td>String</td><td>Required</td></tr>
  <tr><td>name</td><td>String</td><td>Required</td></tr>
  <tr><td>time_zone</td><td>String</td><td>Required</td></tr>
  <tr><td>country_code</td><td>String</td><td>Required. ISO 3166-1 alpha-2.</td></tr>
  <tr><td>locale</td><td>String</td><td>Required. Supported locales: 'en', 'sv' and 'nb'. Default: 'en'.</td></tr>
</table>

#### User Attributes

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>name</td><td>String</td><td>Required</td></tr>
  <tr><td>email</td><td>String</td><td>Required</td></tr>
  <tr><td>password</td><td>String</td><td>Required</td></tr>
  <tr><td>phone_number</td><td>String</td><td>Also available as phonenumber (deprecated).</td></tr>
</table>