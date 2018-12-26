# Invoice Webhook

When a carrier invoice is approved, processed, or marked as an exception
we can POST a JSON payload to an endpoint that you provide us. If you
would like us to authenticate the endpoint you should add a token to the
URL you provide us and verify that is correct.

Each of the payloads will have an event "type" that will allow you to
choose which payloads you want to use and ignore the rest.

* [Approved Payload](#approved-payload)
* [Processed Payload](#processed-payload)
* [Exception Payload](#exception-payload)

## Approved Payload

```
{
  "account": {
    "alias": "account-alias"
  },
  "event": {
    "load_id": "load-id",
    "load": {
      "external_id": "load-external-id" // YOUR internal id
    },
    "type": "approved",
    "id": 7,                            // HubTran's internal id
    "invoice": {
      "number": "invoice-number",
      "date": null,
      "date_to_pay": "1981-08-13",
      "amount_to_pay": 10.2,
      "currency": "USD",
      "quickpay": false,
      "payment_strategy": "direct",     // or "factor"
      "approver": {
        "email": "test@example.com"
      },
      "line_items": [                   // Only present if line items is enabled for the account
        {
          "external_id": "line-item-external-id",
          "description": "line item description",
          "total": 123.45,
          "customer_total": 150.00,
          "quantity": 5,
          "rate": 2.0,
          "type_code": "Line Haul",
          "carrier": {
            "external_id": "carrier-external-id",
          }
        }
      ],
      "extracted_fields": {}            // If custom extracted fields are configured, they'll be here
    },
    "carrier": {
      "external_id": "carrier-external-id"
    },
    "documents": [
      {
        "id": 14,
        "type": "proofOfDelivery",
        "proof_of_delivery": true,
        "url": "https://api.hubtran.com/downloads/documents/unique-id",
        "pages": [
          {"url": "https://api.hubtran.com/downloads/documents/unique-id?format=png&page=1"},
          {"url": "https://api.hubtran.com/downloads/documents/unique-id?format=png&page=2"}
        ],
        "visibility": {
          "carrier": true,
          "customer": true
        }
      }
    ],
    "combined_document_urls": [         // All documents of the same type, combined
      {
        "type": "proofOfDelivery",
        "url": "https://api.hubtran.com/downloads/documents/combined/unique-id",
        "proof_of_delivery": true,
        "pages": [
          {"url": "https://api.hubtran.com/downloads/documents/unique-id?format=png&page=1"},
          {"url": "https://api.hubtran.com/downloads/documents/unique-id?format=png&page=2"}
        ],
        "visibility": {
          "carrier": true,
          "customer": true
        }
      }
    ],
    "remit_to": {
      "name": "name",
      "address_line_1": "address1",
      "address_line_2": "address2",
      "city": "city",
      "state": "state",
      "postal_code": "12345",
      "country": "USA"
    }
  }
}
```

## Processed Payload

Same as the approved payload except the type is "processed" and
"approver" is "processor"

```
"processor": {
  "email": "test@example.com"
}
```

## Exception Payload

```
{
  "account": {
    "alias": "account-alias"
  },
  "event": {
    "load_id": "load-id",
    "load": {
      "external_id": "load-external-id" // YOUR internal id
    },
    "type": "exception",
    "id": 1,
    "invoice": {
      "processor": {
        "email": "test@example.com"
      },
      "extracted_fields": {}              // If custom extracted fields are configured, they'll be here
    },
    "exceptions": [
      {
        "id": 1,
        "message": "exception 1 message",
        "resolution": "not my circus, not my monkeys"
      },
      {
        "id": 2,
        "message": "exception 2 message",
        "resolution": "your circus, your monkeys"
      }
    ],
    "documents": [
      {
        "id": 14,
        "type": "proofOfDelivery",
        "proof_of_delivery": true,
        "url": "https://api.hubtran.com/downloads/documents/unique-id",
        "pages": [
          {"url": "https://api.hubtran.com/downloads/documents/unique-id?format=png&page=1"},
          {"url": "https://api.hubtran.com/downloads/documents/unique-id?format=png&page=2"}
        ],
        "visibility": {
          "carrier": true,
          "customer": true
        }
      }
    ],
    "combined_document_urls": [           // All documents of the same type, combined
      {
        "type": "proofOfDelivery",
        "url": "https://api.hubtran.com/downloads/documents/combined/unique-id",
        "proof_of_delivery": true,
        "pages": [
          {"url": "https://api.hubtran.com/downloads/documents/unique-id?format=png&page=1"},
          {"url": "https://api.hubtran.com/downloads/documents/unique-id?format=png&page=2"}
        ],
        "visibility": {
          "carrier": true,
          "customer": true
        }
      }
    ],
  }
}
```
