# REST API

Very little information is required to actually create objects in
HubTran, often just a name and external_id. However, in order for
HubTran to work well, we need as much information as you can give us.

All dates + times should be in
[iso8601](https://en.wikipedia.org/wiki/ISO_8601) format. All `external_id`s should be strings.

If you receive a status code other than 2xx, please retry your request once with a slight delay - there may be a temporary issue that resolves itself upon retry.

* [Authentication](../rest_authentication.md)
* [Create + Update Loads](#create--update-loads)
* [Load Details](#load-details)
* [Create + Update Shipments](#create--update-shipments)
* [Create + Update Carriers](#create--update-carriers)
* [Bulk Create + Update Carriers](#bulk-create--update-carriers)
* [Create + Update Customer Invoices](#create--update-customer-invoices)
* [Create Payments](#create-payments)
* [Clear Exceptions](#clear-exceptions)
* [List Approved Invoices](#list-approved-invoices)
* [Mark Approved Invoice as Verified](#mark-approved-invoice-as-verified)
* [Mark Approved Invoice as Not Verified](#mark-approved-invoice-as-not-verified)
* [Create Carrier Invoice Submissions](#create-carrier-invoice-submissions)
* [List Documents](#list-documents)
* [Update Account](#update-account)
* [Document Types](#document-types)

## Create + Update Loads

Below you'll see we have "load_id" and "external_id". "load_id" is what the load would be called in the user interface of your application.  Often it is unique and immutable but some TMSs do not have that guarantee. "external_id" is YOUR internal id for the load. "external_id" should always be unique and immutable.

PUT https://api.hubtran.com/tms/loads/:load_id

```
curl -X PUT https://api.hubtran.com/tms/loads/example_id \
  -H "Content-Type: application/json" \
  -H "Authorization: Token token=YOUR_TOKEN" \
  -d '{"load": {"load_id": "example_id", "external_id": "example_id"}}'
```

Request:

```
{
  "load": {
    "load_id": "load-id",                                 // Required
    "external_id": "external-id",                         // Required
    "status": "new",                                      // Recommended
    "brokered": true,
    "tms_created_at": "2016-07-15 19:00:00 +0200",
    "tms_updated_at": "2016-07-17 19:00:00 +0200",
    "target_ship_start": "2016-07-16 12:00:00 +0200",
    "target_ship_end": "2016-07-16 14:00:00 +0200",
    "actual_shipped_at": "2016-07-16 13:00:00 +0200",     // Recommended
    "target_delivery_start": "2016-07-17 19:00:00 +0200",
    "target_delivery_end": "2016-07-17 19:00:00 +0200",
    "actual_delivered_at": "2016-07-17 19:00:00 +0200",   // Recommended
    "require_pod": false,
    "require_weight_ticket": false,
    "require_customer_rate_confirmation": false,
    "require_originals": false,
    "pro_number": "pro-number",
    "quantity": 1.0,
    "weight": 1000,
    "distance": 500,
    "mode": "truck",
    "freight_class": "class",
    "memo": "memo",
    "delivery_number": "delivery-number",
    "ppe": "ppe",
    "quote_number": "quote-number",
    "load_note": "load was stolen",
    "payables_note": "do not pay",
    "office": "Chicago",
    "origin": {                                           // Recommended
      "name": "origin-name",
      "address_line_1": "origin-address-line-1",
      "address_line_2": "origin-address-line-2",
      "city": "origin-city",
      "state": "origin-state",
      "postal_code": "origin-postal-code",
      "country": "origin-country"
    },
    "destination": {                                      // Recommended
      "name": "destination-name",
      "address_line_1": "destination-address-line-1",
      "address_line_2": "destination-address-line-2",
      "city": "destination-city",
      "state": "destination-state",
      "postal_code": "destination-postal-code",
      "country": "destination-country"
    },
    "carrier": {                                          // Recommended
      "name": "carrier-name",
      "scac": "carrier-scac",
      "external_id": "carrier-external-id",
      "dot_number": "dot number",
      "mc_number": "mc number"
    },
    "customer": {                                         // Recommended
      "external_id": "customer-external-id",
      "name": "customer-name",
      "account_number": "customer-account-number"
    },
    "shipments": [                                        // Used to link the load to shipments created via the shipment API
      {
        "external_id": "shipment-external-id"
      }
    ],
    "charges": {                                          // Recommended but ignored if line items are sent
      "total": 1200.23,
      "currency": "USD",
      "line_haul": 1100.32,
      "fuel": 129.32,
      "detention": 22.22,
      "other": 87.87,
      "customer_total": 1500.00
    },
    "picks": [
      {
        "external_id": "pick-external-id",                // Required
        "name": "pick-name",
        "address_line_1": "pick-address-line-1",
        "address_line_2": "pick-address-line-2",
        "city": "pick-city",
        "state": "pick-state",
        "postal_code": "pick-postal-code",
        "country": "pick-country",
        "shipments": [                                    // Required
          {
            "external_id": "shipment-external-id"
          }
        ]
      }
    ],
    "drops": [
      // same format as picks
    ],
    "line_items": [                                       // Recommended, displayed and used to calculate the carrier_charge.
      {
        "external_id": "line_item_1",                     // You're id for matching up line items when sending back to your TMS
        "description": "line item description",
        "total": 123.45,
        "customer_total": 150.00,
        "quantity": 5.0,
        "rate": 2.0,
        "type_code": "abc",
        "carrier": {                                      // Used to calculate carrier_charge for each carrier
          "external_id": "carrier-external-id",
          "scac": "scac",
          "name": "carrier name"
        }
      }
    ],
    "shipped_items": [{
      "external_id": "1234",                              // Required
      "description": "Shipped item",                      // Required
      "class_name": '400',
      "item_type": "CARTON",
      "nmfc": "82270",
      "hazardous_material": false,
      "quantity": 2.0,
      "weight": 417.0,
      "weight_unit": "lb",
      "width": 10.0,
      "height": 12.0,
      "length": 20.0
    }],
    "references": {                                       // Recommended
      "Example Reference Label 1": "Value1",
      "Example Reference Label 2": "Value2",
      // To send multiple references with the same label, comma-separate the values:
      "Example Reference Label 3": "Value3a, Value3b"
      ... as many label-value pairs as you want
    }
  }
}
```

Response:

Same as the response for the [load details](#load-details) API.

## Load Details

GET https://api.hubtran.com/tms/loads/:id

Where :id is the HubTran id you previously saved.

If you don't have the HubTran id you can alternatively use one of the
following forms:

GET https://api.hubtran.com/tms/loads?load_id=:your_load_id
GET https://api.hubtran.com/tms/loads?external_id=:your_load_external_id

load_id is what the user sees in the UI and external_id is what you
would have passed us as your internal, immutable id for the load.
Sometimes they are the same thing.

```
curl -X GET https://api.hubtran.com/tms/loads/example_id \
  -H "Content-Type: application/json" \
  -H "Authorization: Token token=YOUR_TOKEN"
```

Response:

```
{
  "load": {
    "id": 5,                                            // HubTran's internal id for the load
    "load_id": "load-id",                               // The load_id you sent us when creating the load
    "external_id": "external-id",                       // The external_id you sent us when creating the load
    "status": "new",
    "brokered": true,
    "tms_created_at": "2016-07-15 19:00:00 +0200",
    "tms_updated_at": "2016-07-17 19:00:00 +0200",
    "target_delivery_start": "2016-07-17 19:00:00 +0200",
    "target_delivery_end": "2016-07-17 19:00:00 +0200",
    "actual_delivered_at": "2016-07-17 19:00:00 +0200",
    "target_ship_start": "2016-07-16 12:00:00 +0200",
    "target_ship_end": "2016-07-16 14:00:00 +0200",
    "actual_shipped_at": "2016-07-16 13:00:00 +0200",
    "require_pod": false,
    "require_weight_ticket": false,
    "require_customer_rate_confirmation": false,
    "require_originals": false,
    "pro_number": "pro-number",
    "quantity": 1.0,
    "weight": 1000,
    "distance": 500,
    "mode": "truck",
    "freight_class": "class",
    "memo": "memo",
    "delivery_number": "delivery-number",
    "ppe": "ppe",
    "quote_number": "quote-number",
    "load_note": "load was stolen",
    "payables_note": "do not pay",
    "office": "Chicago",
    "origin": {                                 // null if all fields are empty
      "name": "origin-name",
      "address_line_1": "origin-address-line-1",
      "address_line_2": "origin-address-line-2",
      "city": "origin-city",
      "state": "origin-state",
      "postal_code": "origin-postal-code",
      "country": "origin-country"
    },
    "destination": {                            // null if all fields are empty
      "name": "destination-name",
      "address_line_1": "destination-address-line-1",
      "address_line_2": "destination-address-line-2",
      "city": "destination-city",
      "state": "destination-state",
      "postal_code": "destination-postal-code",
      "country": "destination-country"
    },
    "carrier": {                                // null if no carrier
      "name": "carrier-name",
      "scac": "carrier-scac",
      "external_id": "carrier-external-id",
      "dot_number": "dot number",
      "mc_number": "mc number"
    },
    "customer": {                               // null if no customer
      "external_id": "customer-external-id",
      "name": "customer-name",
      "account_number": "customer-account-number"
    },
    "shipments": [                              // Used to link the load to shipments created via the shipment API. Empty array if no shipments.
      {
        "external_id": "shipment-external-id"
      }
    ],
    "charges": {                                // object with null values if null
      "total": 1200.23,
      "currency": "USD",
      "line_haul": 1100.32,
      "fuel": 129.32,
      "detention": 22.22,
      "other": 87.87,
      "customer_total": 1500.00
    },
    "picks": [                                  // empty array if no picks
      {
        "external_id": "pick-external-id",
        "name": "pick-name",
        "address_line_1": "pick-address-line-1",
        "address_line_2": "pick-address-line-2",
        "city": "pick-city",
        "state": "pick-state",
        "postal_code": "pick-postal-code",
        "country": "pick-country",
        "shipments": [
          {
            "external_id": "shipment-external-id"
          }
        ]
      }
    ],
    "drops": [                                  // empty array if no drops
      // same format as picks
    ],
    "line_items": [                             // Displayed and used to calculate the carrier_charge. Empty array if no line items.
      {
        "external_id": "line_item_1",           // You're id for matching up line items when sending back to your TMS
        "description": "line item description",
        "total": 123.45,
        "customer_total": 150.00,
        "quantity": 5.0,
        "rate": 2.0,
        "type_code": "abc",
        "carrier": {                            // Used to calculate carrier_charge for each carrier
          "external_id": "carrier-external-id",
          "scac": "scac",
          "name": "carrier name"
        }
      }
    ],
    "shipped_items": [                          // empty array if no shipped items
      {
        "external_id": "1234",
        "description": "Shipped item",
        "class_name": '400',
        "item_type": "CARTON",
        "nmfc": "82270",
        "hazardous_material": false,
        "quantity": 2.0,
        "weight": 417.0,
        "weight_unit": "lb",
        "width": 10.0,
        "height": 12.0,
        "length": 20.0
      }
    ],
    "carrier_invoices": [                       // empty array if no invoices
      {
        "number": "invoice-number",
        "state": "approved",
        "date": "1981-08-11",
        "date_to_pay": "1981-08-13",
        "amount_to_pay": "1110.2",
        "currency": "USD",
        "quickpay": false,
        "approver": {
          "email": "test@example.com"
        }
        "carrier": {
          "external_id": "carrier-external-id"
        },
        "documents": [
          {
            "id": 14,
            "type": "proofOfDelivery",
            "proof_of_delivery": true,
            "url": "https://api.hubtran.com/downloads/documents/unique-id",
            "visibility": {
              "carrier": true,
              "customer": true
            },
            "pages": [
              {
                "png_url": "https://api.hubtran.com/downloads/documents/unique-id/pages/xxx.png"
              }
            ]  
          }
        ],
        "combined_document_urls": [             // All documents of the same type, combined
          {
            "type": "proofOfDelivery",
            "url": "https://api.hubtran.com/downloads/documents/combined/unique-id",
            "proof_of_delivery": true,
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
    ],
    "references": {                             // empty object if no references
      "Example Reference Label 1": "Value1",
      "Example Reference Label 2": "Value2",
      "Example Reference Label 3": "Value3a, Value3b"
    },
    "documents": [             // Documents on the load
      {
        "id": 14,
        "type": "proofOfDelivery",
        "proof_of_delivery": true,
        "url": "https://api.hubtran.com/downloads/documents/unique-id",
        "visibility": {
          "carrier": true,
          "customer": true
        },
        "pages": [
          {
            "png_url": "https://api.hubtran.com/downloads/documents/unique-id/pages/xxx.png"
          }
        ]   
      }
    ],
  }
}
```

## Create + Update Shipments

POST https://api.hubtran.com/tms/shipments

```
curl -X POST https://api.hubtran.com/tms/shipments \
  -H "Content-Type: application/json" \
  -H "Authorization: Token token=YOUR_TOKEN" \
  -d '{"shipments": [{"external_id": "example_id"}]}'
```

Request:

```
{
  "shipments": [
    {
      "external_id": "shipment-external-id",              // Required
      "status": "new",                                    // Recommended
      "tms_created_at": "2016-07-10 20:43:00 +0300",
      "tms_updated_at": "2016-07-15 20:43:00 +0300",
      "target_ship_start": "2016-07-16 12:00:00 +0200",
      "target_ship_end": "2016-07-16 14:00:00 +0200",
      "actual_shipped_at": "2016-07-16 13:00:00 +0200",   // Recommended
      "target_delivery_start": "2016-07-17 19:00:00 +0200",
      "target_delivery_end": "2016-07-17 19:00:00 +0200",
      "actual_delivered_at": "2016-07-17 19:00:00 +0200", // Recommended
      "quantity": 1.0,
      "weight": 1000,
      "customer_mode": "FSC and Rate Review",
      "owner": "Owner name",
      "origin": {                                         // Recommended
        "name": "origin-name",
        "address_line_1": "origin-address-line-1",
        "address_line_2": "origin-address-line-2",
        "city": "origin-city",
        "state": "origin-state",
        "postal_code": "origin-postal-code",
        "country": "origin-country"
      },
      "destination": {                                    // Recommended
        "name": "destination-name",
        "address_line_1": "destination-address-line-1",
        "address_line_2": "destination-address-line-2",
        "city": "destination-city",
        "state": "destination-state",
        "postal_code": "destination-postal-code",
        "country": "destination-country"
      },
      "loads": [                                          // Used to link shipments and loads representing a "delivery"
        {
          "external_id": "load-external-id"
        }
      ],
      "charges": {                                        // Recommended
        "total": 1200.23,
        "currency": "USD",
        "line_haul": 1100.32,
        "fuel": 129.32,
        "detention": 22.22,
        "other": 87.87
      },
      "line_items": [
        {
          "external_id": "line_item_1",                   // The line external_id you sent us
          "description": "line item description",
          "total": 123.45,
          "quantity": 5.0,
          "rate": 2.0,
          "type_code": "abc"
        }
      ],
      "shipped_items": [
        {
          "external_id": "1234",                          // Required
          "description": "Shipped item",                  // Required
          "class_name": '400',
          "item_type": "CARTON",
          "nmfc": "82270",
          "hazardous_material": false,
          "quantity": 2.0,
          "weight": 417.0,
          "weight_unit": "lb",
          "width": 10.0,
          "height": 12.0,
          "length": 20.0
        }
      ],
      "references": {                                     // Recommended
        "Example Reference Label 1": "Value1",
        "Example Reference Label 2": "Value2",
        // To send multiple references with the same label, comma-separate the values:
        "Example Reference Label 3": "Value3a, Value3b"
        ... as many label-value pairs as you want
      }
    }
  ]
}
```

Response:

```
{
  "shipments": [
    {
      "id": 1,                                              // HubTran's internal id for the shipment
      "external_id": "shipment-external-id"                 // YOUR internal id for the shipment
      "status": "new",
      "tms_created_at": "2016-07-10 20:43:00 +0300",
      "tms_updated_at": "2016-07-15 20:43:00 +0300",
      "target_ship_start": "2016-07-16 12:00:00 +0200",
      "target_ship_end": "2016-07-16 14:00:00 +0200",
      "actual_shipped_at": "2016-07-16 13:00:00 +0200",
      "target_delivery_start": "2016-07-17 19:00:00 +0200",
      "target_delivery_end": "2016-07-17 19:00:00 +0200",
      "actual_delivered_at": "2016-07-17 19:00:00 +0200",
      "quantity": 1.0,
      "weight": 1000,
      "customer_mode": "FSC and Rate Review",
      "owner": "Owner name",
      "origin": {
        "name": "origin-name",
        "address_line_1": "origin-address-line-1",
        "address_line_2": "origin-address-line-2",
        "city": "origin-city",
        "state": "origin-state",
        "postal_code": "origin-postal-code",
        "country": "origin-country"
      },
      "destination": {
        "name": "destination-name",
        "address_line_1": "destination-address-line-1",
        "address_line_2": "destination-address-line-2",
        "city": "destination-city",
        "state": "destination-state",
        "postal_code": "destination-postal-code",
        "country": "destination-country"
      },
      "loads": [                                            // Used to link shipments and loads representing a "delivery"
        {
          "external_id": "load-external-id"
        }
      ],
      "charges": {
        "total": 1200.23,
        "currency": "USD",
        "line_haul": 1100.32,
        "fuel": 129.32,
        "detention": 22.22,
        "other": 87.87
      },
      "line_items": [
        {
          "external_id": "line_item_1",                     // The line external_id you sent us
          "description": "line item description",
          "total": 123.45,
          "quantity": 5.0,
          "rate": 2.0,
          "type_code": "abc"
        }
      ],
      "shipped_items": [
        {
          "external_id": "1234",          // Required
          "description": "Shipped item",  // Required
          "class_name": '400',
          "item_type": "CARTON",
          "nmfc": "82270",
          "hazardous_material": false,
          "quantity": 2.0,
          "weight": 417.0,
          "weight_unit": "lb",
          "width": 10.0,
          "height": 12.0,
          "length": 20.0
        }
      ],
      "references": {                       // empty object if no references
        "Example Reference Label 1": "Value1",
        "Example Reference Label 2": "Value2",
        "Example Reference Label 3": "Value3a, Value3b"
      }
    }
  ]
}
```

## Create + Update Carriers

PUT https://api.hubtran.com/tms/carriers/:external_id

```
curl -X PUT https://api.hubtran.com/tms/carriers/example_id \
  -H "Content-Type: application/json" \
  -H "Authorization: Token token=YOUR_TOKEN" \
  -d '{"carrier": {"name": "example name", "external_id": "example_id"}}'
```

Request:

```
{
  "carrier": {
    "name": "carrier name",               // Required
    "external_id": "external-id",         // Required
    "external_vendor_id": "evid",         // A reference the user will use to make sure it matches TMS
    "active": true,                       // Recommended
    "scac": "scac",                       // Recommended
    "mc_number": "mc_number",             // Recommended
    "dot_number": "dot_number",
    "ein": "ein",
    "address_name": "Address name",       // Recommended
    "address_line_1": "Address line 1",   // Recommended
    "address_line_2": "Address line 2",   // Recommended
    "city": "City",                       // Recommended
    "state": "State",                     // Recommended
    "postal_code": "Postal code",         // Recommended
    "country": "Country",                 // Recommended
    "account_exec": "Bob",
    "labels": ["LABEL1","LABEL2"],
    "contacts": [                         // Recommended
      "joe@example.com",
      "tom@example.com"
    ],
    "remit_to": {                         // Recommended, should be
factor remit to if carrier is factored
      "name": "Address name",             // Required if remit_to block
is sent
      "address_line_1": "Address line 1",
      "address_line_2": "Address line 2",
      "city": "City",
      "state": "State",
      "postal_code": "Postal code",
      "country": "Country"
    }
  }
}
```

Response:

```
{
  "carrier": {
    "id": 1,                                // HubTran's internal id for the carrier
    "external_id": "carrier-external-id",   // YOUR internal id for the carrier
    "external_vendor_id": "evid",
    "active": true,
    "scac": "scac",
    "mc_number": "mc_number",
    "dot_number": "dot_number",
    "ein": "ein",
    "address_name": "Address name",
    "address_line_1": "Address line 1",
    "address_line_2": "Address line 2",
    "city": "City",
    "state": "State",
    "postal_code": "Postal code",
    "country": "Country",
    "account_exec": "Bob",
    "labels": ["LABEL1","LABEL2"],          // Empty list if there aren't any
labels
    "contacts": [                           // Empty list if there aren't any contacts
      "joe@example.com",
      "tom@example.com"
    ],
    "remit_to": {                           // null if there is no remit to address
      "name": "Address name",
      "address_line_1": "Address line 1",
      "address_line_2": "Address line 2",
      "city": "City",
      "state": "State",
      "postal_code": "Postal code",
      "country": "Country"
    }
  }
}
```

## Bulk Create + Update Carriers

PUT https://api.hubtran.com/tms/carriers/bulk

```
curl -X PUT https://api.hubtran.com/tms/carriers/bulk \
  -H "Content-Type: application/json" \
  -H "Authorization: Token token=YOUR_TOKEN" \
  -d '{"carriers": [{"name": "example name", "external_id": "example_id"}]}'
```

Request:

See [Create + Update Carriers](#create--update-carriers) for the rest of
the supported carrier params.

```
{
  "carriers": [
    {
      "name": "carrier name", // Required
      "external_id": "external-id", // Required
    }
  ]
}
```

Response:

```
{
  "carriers": [
    {
      "carrier": {
        // Same as the single carrier response
      },
      "errors": {} // If this carrier had errors and couldn't be updated, they'll be here
    }
  ]
}

```

## Create + Update Customer Invoices

POST https://api.hubtran.com/tms/customer_invoices/:number

```
curl -X PUT https://api.hubtran.com/tms/customer_invoices \
  -H "Content-Type: application/json" \
  -H "Authorization: Token token=YOUR_TOKEN" \
  -d '{"customer_invoice": {...}}'
```

Request:

```
{
  "customer_invoice": {
    "customer": {
      "external_id": "customer-external-id", // Required
      "name": "example-name"                 // Optional, recommended if new customer
    },
    "number": "invoice-number",              // Required
    "amount": 1000.00,                       // Required
    "date": "2019-04-09",                    // Required, in ISO 8601 format
    "currency": "USD",                       // Optional, in alphabetic ISO 4217 format. Defaults to "USD".
    "invoice_document": {
      data: "base-64-data"                   // Required, in Base64 encoding for MIME
    },
    "shipments": [                           // Required
      {"external_id": "123"},
      {"external_id": "456"}
    ]
  }
}
```

Response:

```
{
  "customer_invoice": {
    "id": 10,                                       // HubTran's internal id for the customer invoice
    "customer": {
      "external_id": "customer-external-id"
    },
    "number": "invoice-number",
    "amount": 1000.00,
    "date": "2019-04-09",
    "currency": "USD",
    "shipments": [
      {"external_id": "123"},
      {"external_id": "456"}
    ]
  }
}
```

## Create Payments

POST https://api.hubtran.com/tms/payments

```
curl -X POST https://api.hubtran.com/tms/payments \
  -H "Content-Type: application/json" \
  -H "Authorization: Token token=YOUR_TOKEN" \
  -d '{"payment": {...}}'
```

Request:

```
{
  "payment": {
    "external_id": "1234",                  // Required
    "amount": 123.1,                        // Required
    "date": "2015-12-09",
    "method": "check",
    "void": true,                           // defaults to false
    "reference": "payment_reference",
    "invoice_number": "invoice_number",     // Optional, but highly desired if it is a payment for an invoice
    "description": "some description",
    "source_system": "your_tms",            // Something other than "hubtran"; Required
    "load": {
      "load_id": "load_id",                 // Required if no load external_id
      "external_id": "load_external_id",    // Required if no load_id
    },
    "carrier": {
      "external_id": "carrier_external_id", // Required
    }
  }
}
```

Response:

```
{
  "payment": {
    "id": 1,                              // HubTran's internal id for the payment
    "external_id": "1234",
    "amount": 123.1,
    "date": "2015-12-09",
    "method": "check",
    "reference": "payment_reference",
    "invoice_number": "invoice_number",
    "description": "some description",
    "source_system": "your_tms",
    "load": {
      "load_id": "load_id",
      "external_id": "load_external_id",
    },
    "carrier": {
      "external_id": "carrier_external_id"
    }
  }
}
```

## Clear Exceptions

POST https://api.hubtran.com/tms/carrier_invoices/:id/exceptions/clear

```
curl -X POST https://api.hubtran.com/tms/carrier_invoices/:id/exceptions/clear \
  -H "Content-Type: application/json" \
  -H "Authorization: Token token=YOUR_TOKEN" \
  -d '{}'
```

Request:

```
{}
```

Response:

```
{
  "ok": true
}
```

## List Approved Invoices

GET https://api.hubtran.com/tms/carrier_invoices/approved

```
curl -X GET https://api.hubtran.com/tms/carrier_invoices/approved \
  -H "Content-Type: application/json" \
  -H "Authorization: Token token=YOUR_TOKEN"
```

Optional URL params:

1. `results_per_page` - number of results to return per page, max 50
2. `page` - which page of results to return based on `results_per_page`

Response:

```
{
  "results_per_page": 20,   // how many results per page
  "page_count": 2,          // number of pages using results_per_page
  "current_page": 1,        // current page
  "carrier_invoices": [
    {
      "id": 123,
      "number": "invoice-number",
      "state": "approved",
      "date": "1981-08-11",
      "date_to_pay": "1981-08-13",
      "amount_to_pay": 1110.2,
      "currency": "USD",
      "quickpay": false,
      "approver": {
        "email": "approver@example.com"
      },
      "processor": {
        "email": "processor@example.com"
      },
      "carrier": {
        "external_id": "carrier-external-id"
      },
      load: {
        "external_id": "load-external-id",
        "load_id": "load-id"
      },
      "line_items": [
        {
          "external_id": "line_item_1",           // The line external_id you sent us
          "description": "line item description",
          "total": 123.45,
          "customer_total": 150.00,
          "quantity": 3.0,
          "rate": 2.0,
          "type_code": "abc",
          "carrier": {
            "external_id": "carrier-external-id",
          }
        }
      ],
      "shipped_items": [{
        "external_id": "1234",
        "description": "Shipped item",
        "class_name": '400',
        "item_type": "CARTON",
        "nmfc": "82270",
        "hazardous_material": false,
        "quantity": 2.0,
        "weight_unit": "lb",
        "weight": 417.0,
        "width": 10.0,
        "height": 12.0,
        "length": 20.0
      }],
      "documents": [
        {
          "id": 14,
          "type": "proofOfDelivery",
          "proof_of_delivery": true,
          "url": "https://api.hubtran.com/downloads/documents/unique-id",
          "visibility": {
            "carrier": true,
            "customer": true
          }
        }
      ],
      "combined_document_urls": [ // All documents of the same type, combined
        {
          "type": "proofOfDelivery",
          "url": "https://api.hubtran.com/downloads/documents/combined/unique-id",
          "proof_of_delivery": true,
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
  ]
}
```

## Mark Approved Invoice as Verified

POST https://api.hubtran.com/tms/carrier_invoices/:id/verified

```
curl -X POST https://api.hubtran.com/tms/carrier_invoices/:id/verified \
  -H "Content-Type: application/json" \
  -H "Authorization: Token token=YOUR_TOKEN"
```

Response:

```
{
  "ok": true
}
```

## Mark Approved Invoice as Not Verified

This is more for when you're developing and you want to reset an invoice
to mark as verified again.

POST https://api.hubtran.com/tms/carrier_invoices/:id/not_verified

```
curl -X POST https://api.hubtran.com/tms/carrier_invoices/:id/not_verified \
  -H "Content-Type: application/json" \
  -H "Authorization: Token token=YOUR_TOKEN"
```

Response:

```
{
  "ok": true
}
```

## Create Carrier Invoice Submissions

If you already have an invoice submission from a carrier (paperwork and/or invoice details)
you may want to programatically flow that into HubTran. This will create an item in the "New" queue
just like if HubTran received an email with paperwork.

For invoices submitted via this API, HubTran will not perform machine learning and data extraction
on the submitted documents. Instead we will use the invoice data you submit, as the sole source of data
about the invoice.

POST https://api.hubtran.com/broker/invoice_submissions

```
curl -X POST https://api.hubtran.com/broker/invoice_submissions \
  -H "Content-Type: application/json" \
  -H "Authorization: Token token=YOUR_TOKEN" \
  -d '{"invoice": {...}}'
```

Request:

```
{
  "invoice": {
    "external_id": "456",    // Required, your internal ID
    "source": "web_billing", // Required
    "number": "111",         // Required
    "amount": 123.0,         // Required
    "date": "2019-04-09",      // Required, in iso8601 format
    "other": {
      "trailer": "333",  // Optional
      "factoring": true, // Optional
    },
    "vendor": {
      "external_id": "789" // Required, the vendor or carrier id that matches id in TMS.
    },
    "load_id": "156", // Required, ID of load in TMS
    "documents": [
      {
        "external_id": "d123",      // Optional, your internal ID for the document.
        "type": "invoice",          // Required, your document type.
        "file_name": "invoice.pdf", // Required
        "data": DATA                // Required, base64-encoded document data
      },
      {
        "external_id": "d456",
        "document_type": "BOL",
        "file_name": "bol.pdf",
        "data": DATA
      }
    ]
  }
}
```

Success Response:

```
HTTP Status Code 201
{
  "invoice": {
    "id": 123 // The HubTran ID of the invoice if you want to store it
  }
}
```

Failure Response:

```
HTTP Status Code 422
{
  "errors": [
    {
      "message": "carrier is missing" // Error message describing the issue.
    }
  ]
}
```

## List Documents

In order to remove documents from this API call you'll have to call the
transmission verification API with the transmission_id for the document

GET https://api.hubtran.com/documents

```
curl -X GET https://api.hubtran.com/documents \
  -H "Content-Type: application/json" \
  -H "Authorization: Token token=YOUR_TOKEN"
```

Optional URL params:

1. `results_per_page` - number of results to return per page, max 50
2. `page` - which page of results to return based on `results_per_page`

Response:

```
{
  "results_per_page": 20,   // how many results per page
  "page_count": 2,          // number of pages using results_per_page
  "current_page": 1,        // current page
  "documents": [
    {
      "id": 123,
      "transmission_id": 456,
      "load": {
        "external_id": "LOAD123"
      },
      "type": "proofOfDelivery",
      "proof_of_delivery": true,
      "url": "https://api.hubtran.com/downloads/documents/unique-id",
      "visibility": {
        "carrier": true,
        "customer": true
      }
    }
  ]
}
```

## Mark Transmission as Verified

POST https://api.hubtran.com/tms/transmissions/:id/verified

```
curl -X POST https://api.hubtran.com/tms/transmissions/:id/verified \
  -H "Content-Type: application/json" \
  -H "Authorization: Token token=YOUR_TOKEN"
```

Response:

```
{
  "ok": true
}
```

## Update Account

PUT https://api.hubtran.com/accounts/ours

```
curl -X PUT https://api.hubtran.com/accounts/ours \
  -H "Content-Type: application/json" \
  -H "Authorization: Token token=YOUR_TOKEN" \
  -d '{"account": {"webhook_url": "http://example.com"}}'
```

Request:

```
{
  "account": {
    "webhook_url": "http://example.com"
  }
}
```

Response:

```
{
  "account": {
    "webhook_url": "http://example.com"
  }
}
```

## Document Types

This is more of a utility endpoint so it's easy for you to see
what kinds of document type values you will be receiving via webhook.

GET https://api.hubtran.com/document_types

```
curl -X GET https://api.hubtran.com/document_types \
  -H "Content-Type: application/json" \
  -H "Authorization: Token token=YOUR_TOKEN"
```

Response:

```
{
  "enabled": [  // document types currently enabled for the account
    {"name": "Bill of Lading / Packing Slip", "value":"billOfLading"},
    // ...
  ],
  "all": [      // all the document types that COULD be enabled
    {"name": "Bill of Lading / Packing Slip", "value":"billOfLading"},
    // ...
  ]
}
```
