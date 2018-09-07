# REST API

Very little information is required to actually create objects in
HubTran, often just a name and external_id. However, in order for
HubTran to work well, we need as much information as you can give us.

All dates + times should be in
[iso8601](https://en.wikipedia.org/wiki/ISO_8601) format. All `external_id`s should be strings.

* [Authentication](../rest_authentication.md)
* [Create + Update Debtors](#create--update-debtors)
* [Bulk Create + Update Debtors](#bulk-create--update-debtors)
* [Invoice Purchased](#invoice-purchased)
* [Invoice Declined](#invoice-declined)

## Create + Update Debtors

PUT https://api.hubtran.com/tms/debtors/:external_id

```
curl -X PUT https://api.hubtran.com/tms/debtors/example_id \
  -H "Content-Type: application/json" \
  -H "Authorization: Token token=YOUR_TOKEN" \
  -d '{"debtor": {"name": "example name", "external_id": "example_id"}}'
```

Request:

```
{
  "debtor": {
    "name": "debtor name",                // Required
    "external_id": "external-id",         // Required
    "active": true,                       // Recommended
    "mc_number": "123456",                // Recommended
    "no_buy": false,                      // if excluded default is false
    "remit_to": {                         // Recommended
      "name": "Address name",             // Required if sending a remit to address
      "address_line_1": "Address line 1",
      "address_line_2": "Address line 2",
      "city": "City",
      "state": "State",
      "postal_code": "Postal code",
      "country": "Country",
      "contact": {
        "email": "joe@example.com"
      }
    }
  }
}
```

Response:

```
{
  "debtor": {
    "id": 1,                              // HubTran's internal id for the debtor
    "external_id": "debtor-external-id",  // YOUR internal id for the debtor
    "name": "debtor name",
    "active": true,
    "mc_number": "123456",
    "no_buy": false,
    "remit_to": {
      "name": "Address name",             // Required if sending a remit to address
      "address_line_1": "Address line 1",
      "address_line_2": "Address line 2",
      "city": "City",
      "state": "State",
      "postal_code": "Postal code",
      "country": "Country",
      "contact": {
        "email": "joe@example.com"
      }
    },
    errors: {}
  }
}

```

## Bulk Create + Update Debtors

PUT https://api.hubtran.com/tms/debtors/bulk

```
curl -X PUT https://api.hubtran.com/tms/debtors/bulk \
  -H "Content-Type: application/json" \
  -H "Authorization: Token token=YOUR_TOKEN" \
  -d '{"debtors": [{"name": "example name", "external_id": "example_id"}]}'
```

Request:

See [Create + Update Debtors](#create--update-debtors) for the rest of
the supported debtor params.

```
{
  "debtors": [
    {
      "name": "debtor name",        // Required
      "external_id": "external-id"  // Required
    }
  ]
}
```

Response:

```
{
  "debtors": [
    // same as the single debtor response above
  ]
}

```

## Invoice Purchased

POST https://api.hubtran.com/tms/invoices/:hubtran_id/purchase

```
curl -X PUT https://api.hubtran.com/tms/invoices/example_id/purchase \
  -H "Content-Type: application/json" \
  -H "Authorization: Token token=YOUR_TOKEN" \
  -d '{"ignored": "true"}'
```

Request:

The `hubtran_id` in the URL is the value of the `hubtran_id` field in the payload we send you.

Response:

Empty JSON on success, error status code on failure

## Invoice Declined

POST https://api.hubtran.com/tms/invoices/:hubtran_id/decline

```
curl -X PUT https://api.hubtran.com/tms/invoices/example_id/decline \
  -H "Content-Type: application/json" \
  -H "Authorization: Token token=YOUR_TOKEN" \
  -d '{"ignored": "true"}'
```

Request:

The `hubtran_id` in the URL is the value of the `hubtran_id` field in the payload we send you.

Response:

Empty JSON on success, error status code on failure


## Verifications

POST https://api.hubtran.com/verifications

```
curl -X POST https://api.hubtran.com/verifications \
  -H "Content-Type: application/json" \
  -H "Authorization: Token token=YOUR_TOKEN" \
  -d '{"amount":100.00, "load_id":"123", "debtor":{"external_id":"456"}, "carrier":{"external_id":"789"}}'
```

Request:

All parameters are required.

Response:

```js
{
  "state": "success",            // Required
  "disclosure_details": {        // Optional, may be `null`
    "amount_mismatch_failure": {
      "broker_amount": 50.0      // Optional additional data
    }
  }
}
```

#### Possible `state`

| Value |
| ----- |
| `pending` |
| `success` |
| `fail` |
| `non_participating_carrier` |
| `non_participating_broker` |

In the case of a `pending` response,
you should retry the same request after 1 minute.
In the event that the verification data is still unavailable after 5 attempts,
you can consider this a failure in our service.
We monitor for these issues, and they should rarely occur.

#### Possible `disclosure_details`

This field provides more detailed information in the `fail` case.
It is `null` for all other states.

Verifications can fail for multiple reasons,
so the value object will have _at least_ one key.
Keys may be added in the future for new failure types.

Each failure _may_ provide additional data within the JSON value.
Each additional data field is _optional_.
Keys may be added in the future for new failure data.

| Key | Additional data fields |
| --- | ----- |
| `amount_mismatch_failure` | `broker_amount` — NUMERIC STRING, ex. `"99.99"` |
| `undelivered_failure` | none |
| `load_issue_failure` | none |
| `load_not_found_failure` | none |
| `carrier_mismatch_failure` | none |
| `possible_fees_failure` | none |
| `remit_to_failure` | none |
