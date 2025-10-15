# Webhook Examples

Please familiarise yourself with the key concepts of the Makeplans API first.

## Event examples

### Booking.confirmed

Triggers when a booking is confirmed.

<details>
<summary>Request</summary>

```http
POST /makeplans-events HTTP/1.1
Host: example.org
Content-Type: application/json; charset=utf-8
User-Agent: Makeplans API
X-MakePlans-Signature: sha256=foobar
```

```json
{
  "event": "booking.confirmed",
  "idempotency_id": "0fc2da72-670f-48d8-98e8-dd181c85c2ee",
  "generated_at": "2024-12-04T12:27:53+01:00",
  "performed_by": {"user": {"id": 1337, "name": "Mr Smith"}},
  "data": {
    "object": "booking",
    "id": 1,
    "booking": {
      "booked_from": "2012-09-29T07:00:00+02:00",
      "booked_to": "2012-09-29T08:00:00+02:00",
      "created_at": "2012-09-20T15:34:16+02:00",
      "custom_data": null,
      "count": 1,
      "expires_at": null,
      "external_id": null,
      "id": 1,
      "notes": "Very handsome customer",
      "person_id": 1,
      "resource_id": 1,
      "service_id": 1,
      "state": "confirmed",
      "status": null,
      "updated_at": "2012-09-20T15:34:16+02:00"
    }
  }
}
```
</details>