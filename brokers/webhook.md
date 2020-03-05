# Invoice Webhook

When a carrier invoice is approved or marked as an exception
we can POST a JSON payload to an endpoint that you provide us. If you
would like us to authenticate the endpoint you should add a token to the
URL you provide us and verify that is correct.

Each of the payloads will have an event "type" that will allow you to
choose which payloads you want to use and ignore the rest.

* [Approved Payload](#approved-payload)
* [Exception Payload](#exception-payload)
* [Notes](#notes)

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
    "invoice": {
      "id": 7,                            // HubTran's internal id
      "number": "invoice-number",
      "date": "2019-07-13",
      "date_to_pay": "2019-08-13",
      "amount_to_pay": 10.2,
      "source_invoice_amount": 15.2,
      "currency": "USD",
      "approved_at": "2020-01-22T00:00:00.000+00:00",
      "quickpay": false,
      "payment_strategy": "direct",     // or "factor"
      "load": {
        "external_id": "load-external-id", // YOUR internal id
        "shipments": [
          {
            "external_id": "shipment-external-id",
            "documents": [{
              "id": 14,
              "type": "proofOfDelivery",
              "proof_of_delivery": true,
              "url": "https://api.hubtran.com/downloads/documents/unique-id",
              "pages": [
                {"png_url": "https://api.hubtran.com/downloads/documents/unique-id/pages/123.png"},
                {"png_url": "https://api.hubtran.com/downloads/documents/unique-id/pages/456.png"}
              ],
              "visibility": {
                "carrier": true,
                "customer": true // based on the shipment customer if
present
              }
            }],
            "combined_document_urls": [
              {
                "type": "proofOfDelivery",
                "url": "https://api.hubtran.com/downloads/documents/combined/unique-id",
                "proof_of_delivery": true,
                "pages": [
                  {"png_url": "https://api.hubtran.com/downloads/documents/unique-ids/pages/123.png"},
                  {"png_url": "https://api.hubtran.com/downloads/documents/unique-ids/pages/456.png"}
                ],
                "visibility": {
                  "carrier": true,
                  "customer": true // based on the shipment customer if present
                }
              }
            ]
          }
        ]
      },
      "approver": {
        "email": "approver@example.com"
      },
      "processor": {
        "email": "processor@example.com"
      },
      "line_items": [                   // Only present if line items is enabled for the account
        {
          "external_id": "line-item-external-id",
          "description": "line item description",
          "total": 123.45,
          "customer_total": 150.00,
          "quantity": 5.0,
          "rate": 2.0,
          "type_code": "Line Haul",
          "carrier": {
            "external_id": "carrier-external-id",
          }
        }
      ],
      "extracted_fields": {},            // If custom extracted fields are configured, they'll be here
      "email_messages": [
        {
          "subject": "subject of email 1",
          "received_at": "2020-03-05T00:00:00.000+00:00"
        },
        {
          "subject": "subject of email 2",
          "received_at": "2020-03-06T00:00:00.000+00:00"
        }
      ]
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
          {"png_url": "https://api.hubtran.com/downloads/documents/unique-id/pages/123.png"},
          {"png_url": "https://api.hubtran.com/downloads/documents/unique-id/pages/456.png"}
        ],
        "visibility": {
          "carrier": true,
          "customer": true // based on the load customer
        },
        "load": {"external_id": "load-external-id"},
        "shipments": [
          {"external_id": "shipment-external-id"}
        ]
      }
    ],
    "combined_document_urls": [         // All documents of the same type, combined
      {
        "type": "proofOfDelivery",
        "url": "https://api.hubtran.com/downloads/documents/combined/unique-id",
        "proof_of_delivery": true,
        "pages": [
          {"png_url": "https://api.hubtran.com/downloads/documents/unique-ids/pages/123.png"},
          {"png_url": "https://api.hubtran.com/downloads/documents/unique-ids/pages/456.png"}
        ],
        "visibility": {
          "carrier": true,
          "customer": true // based on the load customer
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
    "invoice": {
      "id": 7,                            // HubTran's internal id
      "processor": {
        "email": "test@example.com"
      },
      "extracted_fields": {},              // If custom extracted fields are configured, they'll be here
      "email_messages": [
        {
          "subject": "subject of email 1",
          "received_at": "2020-03-05T00:00:00.000+00:00"
        },
        {
          "subject": "subject of email 2",
          "received_at": "2020-03-06T00:00:00.000+00:00"
        }
      ]
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
          {"png_url": "https://api.hubtran.com/downloads/documents/unique-id/pages/123.png"},
          {"png_url": "https://api.hubtran.com/downloads/documents/unique-id/pages/456.png"}
        ],
        "visibility": {
          "carrier": true,
          "customer": true // based on the load customer
        }
      }
    ],
    "combined_document_urls": [           // All documents of the same type, combined
      {
        "type": "proofOfDelivery",
        "url": "https://api.hubtran.com/downloads/documents/combined/unique-id",
        "proof_of_delivery": true,
        "pages": [
          {"png_url": "https://api.hubtran.com/downloads/documents/unique-ids/pages/123.png"},
          {"png_url": "https://api.hubtran.com/downloads/documents/unique-ids/pages/456.png"}
        ],
        "visibility": {
          "carrier": true,
          "customer": true // based on the load customer
        }
      }
    ],
  }
}
```

## Notes

### Tiff Documents
HubTran can optionally enable documents to be fetched in tiff format. If this setting is enabled
there will be a "tiff_url" alongside the "url" in "documents" and "combined_document_urls".
It will look like this:
```
    "documents": [
      {
        "id": 14,
        "type": "proofOfDelivery",
        "proof_of_delivery": true,
        "url": "https://api.hubtran.com/downloads/documents/unique-id",
        "tiff_url": "https://api.hubtran.com/downloads/documents/unique-id.tiff",
        "pages": [
          {"png_url": "https://api.hubtran.com/downloads/documents/unique-id/pages/123.png"},
          {"png_url": "https://api.hubtran.com/downloads/documents/unique-id/pages/456.png"}
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
        "tiff_url": "https://api.hubtran.com/downloads/documents/combined/unique-id.tiff",
        "proof_of_delivery": true,
        "pages": [
          {"png_url": "https://api.hubtran.com/downloads/documents/unique-ids/pages/123.png"},
          {"png_url": "https://api.hubtran.com/downloads/documents/unique-ids/pages/456.png"}
        ],
        "visibility": {
          "carrier": true,
          "customer": true
        }
      }
    ],
```
