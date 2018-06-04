# REST API

Very little information is required to actually create objects in
HubTran, often just a name and external_id. However, in order for
HubTran to work well, we need as much information as you can give us.

All dates + times should be in
[iso8601](https://en.wikipedia.org/wiki/ISO_8601) format. All `external_id`s should be strings.

* [Authentication](../rest_authentication.md)
* [Create + Update Loads](#create--update-loads)
* [Load Details](#load-details)
* [Create + Update Shipments](#create--update-shipments)
* [Create + Update Carriers](#create--update-carriers)
* [Bulk Create + Update Carriers](#bulk-create--update-carriers)
* [Create Payments](#create-payments)
* [Document Upload](#document-upload)
* [Clear Exceptions](#clear-exceptions)
* [List Approved Invoices](#list-approved-invoices)
* [Mark Approved Invoice as Received](#mark-approved-invoice-as-received)

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
    "load_id": "load-id", // Required
    "external_id": "external-id", // Required
    "status": "new",
    "tms_created_at": "2016-07-15 19:00:00 +0200",
    "tms_updated_at": "2016-07-17 19:00:00 +0200",
    "target_ship_start": "2016-07-16 12:00:00 +0200",
    "target_ship_end": "2016-07-16 14:00:00 +0200",
    "actual_shipped_at": "2016-07-16 13:00:00 +0200",
    "target_delivery_start": "2016-07-17 19:00:00 +0200",
    "target_delivery_end": "2016-07-17 19:00:00 +0200",
    "actual_delivered_at": "2016-07-17 19:00:00 +0200",
    "require_pod": false,
    "require_weight_ticket": false,
    "require_customer_rate_confirmation": false,
    "require_originals": false,
    "pro_number": "pro-number",
    "primary_reference": "primary-reference",
    "primary_reference_type": "primary-reference-type",
    "quantity": 1,
    "weight": 1000,
    "distance": "500",
    "mode": "truck",
    "freight_class": "class",
    "po": "po",
    "pickup_reference": "pickup-reference",
    "memo": "memo",
    "tender_user": "jim",
    "delivery_number": "delivery-number",
    "ppe": "ppe",
    "quote_number": "quote-number",
    "ship_ref": "ship-ref",
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
    "carrier": {
      "name": "carrier-name",
      "scac": "carrier-scac",
      "external_id": "carrier-external-id"
    },
    "customer": {
      "external_id": "customer-external-id",
      "name": "customer-name",
      "account_number": "customer-account-number"
    },
    "shipments": [ // Used to link the load to shipments created via the shipment API
      {
        "external_id": "shipment-external-id"
      }
    ],
    "charges": { // Ignored if line items are sent
      "total": 1200.23,
      "currency": "USD",
      "line_haul": 1100.32,
      "fuel": 129.32,
      "detention": 22.22,
      "other": 87.87
    },
    "picks": [
      {
        "external_id": "pick-external-id", // Required
        "name": "pick-name",
        "address_line_1": "pick-address-line-1",
        "address_line_2": "pick-address-line-2",
        "city": "pick-city",
        "state": "pick-state",
        "postal_code": "pick-postal-code",
        "country": "pick-country",
        "shipments": [ // Required
          {
            "external_id": "shipment-external-id"
          }
        ]
      }
    ],
    "drops": [
      // same format as picks
    ],
    "line_items": [ // Displayed and used to calculate the carrier_charge.
      {
        "description": "line item description",
        "total": 123.45,
        "quantity": 5,
        "carrier": { // Used to calculate carrier_charge for each carrier
          "external_id": "carrier-external-id",
          "scac": "scac",
          "name": "carrier name"
        }
      }
    ],
    "shipped_items": [{
      "external_id": "1234", // Required
      "description": "Shipped item", // Required
      "weight_unit": "lb",
      "quantity": 2.0,
      "item_type": "CARTON",
      "hazardous_material": false,
      "weight": 417.0,
      "class_name": '400',
      "nmfc": "82270",
      "width": 10.0,
      "height": 12.0,
      "length": 20.0
    }],
    "references": {
      "Key": "Value",
      "Key2": "Value2,Value3",
      "Anything": "You Want"
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
    "id": 5, // HubTran's internal id for the load
    "load_id": "load-id", // The load_id you sent us when creating the load
    "external_id": "external-id", // The external_id you sent us when creating the load
    "status": "new",
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
    "primary_reference": "primary-reference",
    "primary_reference_type": "primary-reference-type",
    "quantity": 1,
    "weight": 1000,
    "distance": "500",
    "mode": "truck",
    "freight_class": "class",
    "po": "po",
    "pickup_reference": "pickup-reference",
    "memo": "memo",
    "tender_user": "jim",
    "delivery_number": "delivery-number",
    "ppe": "ppe",
    "quote_number": "quote-number",
    "ship_ref": "ship-ref",
    "origin": { // null if all fields are empty
      "name": "origin-name",
      "address_line_1": "origin-address-line-1",
      "address_line_2": "origin-address-line-2",
      "city": "origin-city",
      "state": "origin-state",
      "postal_code": "origin-postal-code",
      "country": "origin-country"
    },
    "destination": { // null if all fields are empty
      "name": "destination-name",
      "address_line_1": "destination-address-line-1",
      "address_line_2": "destination-address-line-2",
      "city": "destination-city",
      "state": "destination-state",
      "postal_code": "destination-postal-code",
      "country": "destination-country"
    },
    "carrier": { // null if no carrier
      "name": "carrier-name",
      "scac": "carrier-scac",
      "external_id": "carrier-external-id"
    },
    "customer": { // null if no customer
      "external_id": "customer-external-id",
      "name": "customer-name",
      "account_number": "customer-account-number"
    },
    "shipments": [ // Used to link the load to shipments created via the shipment API. Empty array if no shipments.
      {
        "external_id": "shipment-external-id"
      }
    ],
    "charges": { // object with null values if null
      "total": 1200.23,
      "currency": "USD",
      "line_haul": 1100.32,
      "fuel": 129.32,
      "detention": 22.22,
      "other": 87.87
    },
    "picks": [ // empty array if no picks
      {
        "external_id": "pick-external-id", // Required
        "name": "pick-name",
        "address_line_1": "pick-address-line-1",
        "address_line_2": "pick-address-line-2",
        "city": "pick-city",
        "state": "pick-state",
        "postal_code": "pick-postal-code",
        "country": "pick-country",
        "shipments": [ // Required
          {
            "external_id": "shipment-external-id"
          }
        ]
      }
    ],
    "drops": [ // empty array if no drops
      // same format as picks
    ],
    "line_items": [ // Displayed and used to calculate the carrier_charge. Empty array if no line items.
      {
        "description": "line item description",
        "total": 123.45,
        "quantity": 5,
        "carrier": { // Used to calculate carrier_charge for each carrier
          "external_id": "carrier-external-id",
          "scac": "scac",
          "name": "carrier name"
        }
      }
    ],
    "shipped_items": [ // empty array if no shipped items
      {
        "external_id": "1234", // Required
        "description": "Shipped item", // Required
        "weight_unit": "lb",
        "quantity": 2.0,
        "item_type": "CARTON",
        "hazardous_material": false,
        "weight": 417.0,
        "class_name": '400',
        "nmfc": "82270",
        "width": 10.0,
        "height": 12.0,
        "length": 20.0
      }
    ],
    "carrier_invoices": [ // empty array if no invoices
      {
        "number": "invoice-number",
        "state": "approved",
        "date": "1981-08-11",
        "date_to_pay": "1981-08-13",
        "amount_to_pay": "1110.2",
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
    ],
    "references": { // empty object if no references
      "Key": "Value",
      "Key2": "Value2,Value3",
      "Anything": "You Want"
    }
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
      "external_id": "shipment-external-id", // Required
      "status": "new",
      "tms_created_at": "2016-07-10 20:43:00 +0300",
      "tms_updated_at": "2016-07-15 20:43:00 +0300",
      "target_ship_start": "2016-07-16 12:00:00 +0200",
      "target_ship_end": "2016-07-16 14:00:00 +0200",
      "actual_shipped_at": "2016-07-16 13:00:00 +0200",
      "target_delivery_start": "2016-07-17 19:00:00 +0200",
      "target_delivery_end": "2016-07-17 19:00:00 +0200",
      "actual_delivered_at": "2016-07-17 19:00:00 +0200",
      "quantity": 1,
      "weight": 1000,
      "customer_mode": "FSC and Rate Review",
      "type": "REGULAR",
      "owner": "Owner name",
      "po": "po",
      "pickup_reference": "pickup-reference",
      "ship_ref": "C16091",
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
      "loads": [ // Used to link shipments and loads representing a "delivery"
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
      "id": 1, // HubTran's internal id for the shipment
      "external_id": "shipment-external-id" // YOUR internal id for the shipment
      "status": "new",
      "tms_created_at": "2016-07-10 20:43:00 +0300",
      "tms_updated_at": "2016-07-15 20:43:00 +0300",
      "target_ship_start": "2016-07-16 12:00:00 +0200",
      "target_ship_end": "2016-07-16 14:00:00 +0200",
      "actual_shipped_at": "2016-07-16 13:00:00 +0200",
      "target_delivery_start": "2016-07-17 19:00:00 +0200",
      "target_delivery_end": "2016-07-17 19:00:00 +0200",
      "actual_delivered_at": "2016-07-17 19:00:00 +0200",
      "quantity": 1,
      "weight": 1000,
      "customer_mode": "FSC and Rate Review",
      "type": "REGULAR",
      "owner": "Owner name",
      "po": "po",
      "pickup_reference": "pickup-reference",
      "ship_ref": "C16091",
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
      "loads": [ // Used to link shipments and loads representing a "delivery"
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
    "name": "carrier name", // Required
    "external_id": "external-id", // Required
    "external_vendor_id": "evid", // A reference the user will use to make sure it matches TMS
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
    "labels": ["LABEL1","LABEL2"],
    "contacts": [ // Helps us associate carriers and incoming invoices
      "joe@example.com",
      "tom@example.com"
    ],
    "remit_to": {
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

Response:

```
{
  "carrier": {
    "id": 1, // HubTran's internal id for the carrier
    "external_id": "carrier-external-id", // YOUR internal id for the carrier
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
    "labels": ["LABEL1","LABEL2"], // Empty list if there aren't any
labels
    "contacts": [ // Empty list if there aren't any contacts
      "joe@example.com",
      "tom@example.com"
    ],
    "remit_to": { // null if there is no remit to address
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
    "external_id": "1234", // Required
    "amount": 123.1, // Required
    "date": "2015-12-09",
    "method": "check",
    "void": true, // defaults to false
    "reference": "payment_reference",
    "invoice_number": "invoice_number", // Optional, but highly desired if it is a payment for an invoice
    "description": "some description",
    "source_system": "your_tms", // something other than "hubtran"; Required
    "load": {
      "load_id": "load_id", // Required if no load external_id
      "external_id": "load_external_id", // Required if no load_id
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
    "id": 1, // HubTran's internal id for the payment
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

## Document Upload

Please let us know if you would like to use this API, as it needs to be enabled for your account.


POST https://api.hubtran.com/tms/documents

```
curl -X POST https://api.hubtran.com/tms/documents \
  -H "Content-Type: application/json" \
  -H "Authorization: Token token=YOUR_TOKEN" \
  -d '{"document":{"load_id": "1234","type":"invoice","url":"url of image"}}'
```

Request:

```
{
  "document": {
    "load_id": "2134",
    "type": "rateConfirmation",
    "url": "URL of image to download. Required unless data is provided",
    "data": "base64 encoded data for image. Required unless url is provided"
  }
}
```

Response:

On success:
```
{}
```

On Failure:

```
{
  "errors": ["list of errors"]
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
  "results_per_page": 20, // how many results per page
  "page_count": 2, // number of pages using results_per_page
  "current_page": 1, // current page
  "carrier_invoices": [
    {
      "id": 123,
      "number": "invoice-number",
      "state": "approved",
      "date": "1981-08-11",
      "date_to_pay": "1981-08-13",
      "amount_to_pay": "1110.2",
      "quickpay": false,
      "approver": {
        "email": "test@example.com"
      },
      "carrier": {
        "external_id": "carrier-external-id"
      },
      load: {
        "external_id": "load-external-id"
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

## Mark Approved Invoice as Received

POST https://api.hubtran.com/tms/carrier_invoices/:id/received

```
curl -X POST https://api.hubtran.com/tms/carrier_invoices/:id/received \
  -H "Content-Type: application/json" \
  -H "Authorization: Token token=YOUR_TOKEN"
```

Response:

```
{
  "ok": true
}
```
