---
title: Date Handling
nav_order: 4
---

All dates are specified in the ISO 8601 format. Time zone is included in the output and specified by the account. It is not necessary to specify time zone in the input as the account time zone will be used as default. The output will give a full ISO 8601 date format with time zone: `YYYY-MM-DDThh:mm:ssTZD`. For input we recommend that you do not specify time zone unless needed and omit seconds: `YYYY-MM-DD hh:mm`.

## Examples

Output from the API:

```
2024-09-29T14:30:00+02:00
```

Recommended input format:

```
2024-09-29 14:30
```

Input with explicit time zone (if needed):

```
2024-09-29T14:30:00+02:00
```

For date-only fields such as `exception_date` and `date_of_birth`, use the format `YYYY-MM-DD`:

```
2024-09-29
```
