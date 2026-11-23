---
title: Get Started
nav_order: 1
description: Get started with the Makeplans API — sign up for a test account, enable the API and get your API-Key.
---

1) Read this documentation.

2) [Sign up for a trial account in our test environment](https://app.test.makeplans.net/client/new){:target="_blank"}

3) Go to Setup -> Integrations -> API. Enable the API and get the API-key.

4) Profit.

When your integration is ready to be released you can sign up for a [real account](https://app.makeplans.com/client/new){:target="_blank"}.

Limitations of the test environment:
* SMS reminders are not triggered.
* Very low SMS quota.
* Requests may run slower.
* Bugs may occur during roll out of new features to be tested.
* Lower uptime.
* All financial transactions such as Stripe are performed in their respective test/sandbox environment. Other integrations are performed against real production accounts unless the integration offers a test environment.

## Test your setup

Once you have your account subdomain and your API-Key, confirm they line up with a single request:

```shell
curl -H 'Authorization: Bearer APIKEY' \
-H 'User-Agent: YourAppName' \
-H 'Accept: application/json' \
https://youraccount.test.makeplans.net/api/v1/client
```

On success it returns `200` with the [account](/endpoints/account/) your API-Key resolves to:

```json
{
  "client": {
    "subdomain": "youraccount",
    "name": "Your account",
    ...
  }
}
```

A `401` means the API-Key (or header) is wrong, or the API is not enabled on the account. A redirect (`301`) means the subdomain does not match an account.
