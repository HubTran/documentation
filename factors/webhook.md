# Invoice Webhook

When a invoice is approved we can POST a JSON payload to an endpoint that
you provide us. If you would like us to authenticate the endpoint you should
add a token to the URL you provide us and verify that is correct.

Each of the payloads will have an event "type" that will allow you to choose which
payloads you want to use and ignore the rest.

* [Approved Payload](#approved-payload)

## Approved Payload

```
{
  "account": {
    "alias": "account-alias"
  },
  "event": {
    "type": "approved",
    "batch_total": "$500.00",
    "batch_count": 1,
    "client_name": "Client ABC",
    "client_code": "abc123",
    "invoices": [
      {
        "hubtran_id": 123,
        "invoice_number": "ABC123",
        "invoice_date": "2017-04-06T19:24:49Z",
        "invoice_amount": "$500.00",
        "po_number": "123",
        "days_due": 30,
        "extracted_fields": {
          "key": "value"
        },
        "audit_flags": [
          {
            "code": "123",
            "flag_checked": false,
            "custom_message": "custom message"
          }
        ],
        "description": "description",
        "debtor": {
          "debtor_code": "some-debtor",
          "address": {
            "addr1": "123 Some Road",
            "addr2": "Ste 202",
            "city": "Chicago",
            "state": "IL",
            "postal_code": "12345"
          },
          "additional_info": {
            "email": "test@example.com",
            "phone": "1234567890",
            "fax": null,
            "attention": null
          }
        },
        "approver_email": approver@example.com,
        "processed_by_email": "processor@example.com",
        "notes": null,
        "origin": {
          "name": "Some Factory",
          "city": "Chicago",
          "state": IL,
          "postal_code": 12345,
          "lat": "35.2783099",
          "lng": "-85.8791074"
        },
        "destination": {
          "name": "Some Customer",
          "city": "Chicago",
          "state": IL,
          "postal_code": 12345,
          "lat": "37.2783099",
          "lng": "-87.8791074"
        },
        "images": [
          {
            "document_type": "rate_confirmation",
            "visibility": {
              "client": true,
              "debtor": true
            },
            "image_information": {
              "urls": {
                "pdf": "http://example.com/url/to/pdf"
              }
            }
          },
          {
            "document_type": "bill_of_lading",
            "visibility": {
              "client": true,
              "debtor": true
            },
            "image_information": {
              "urls": {
                "pdf": "http://example.com/url/to/pdf"
              }
            }
          }
        ],
        verification: {
          v1: {
            {
              "state": "success",            // Required
              "disclosure_details": {        // Optional, may be `null`
                "amount_mismatch_failure": {
                  "broker_amount": "50.0"    // Optional additional data
                }
              }
            }
          }
        }
      }
    ],
    "non_invoice_images": [
      {
        "document_type": "audit_sheet",
        "visibility": {
          "client": false,
          "debtor": false
        }
      }
    ]
  }
}
```
