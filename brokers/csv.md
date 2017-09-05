# CSV

All files should be sent in CSV format. The name of the file doesn't
matter, but **the subject lines must exactly match**.

All dates + times should be in
[iso8601](https://en.wikipedia.org/wiki/ISO_8601) format.

Your email should be sent to the same address you send invoices to.

Required fields are **bold**.

* [Loads](#loads)
* [Shipments](#shipments)
* [Carriers](#carriers)
* [Invoice Approval Response](#invoice-approval-response)

## Loads

We recommend that you send an email with Load details three
times a day at 6am, 10am and 1pm. We ask that you pick a random number
of minutes or before the hour to help us spread processing a bit. You
may send Loads as infrequently as once per day, but it will
increase the likelihood of a necessary load not being in the system.

The subject of the email must exactly match `tms_load:<<API TOKEN>>`

* **load_id** - Shows up in the UI, needs to be unique
* **external_id** - needs to be unique
* **carrier_external_id**
* carrier_name
* carrier_scac
* origin_name
* origin_address_line_1
* origin_address_line_2
* origin_city
* origin_state
* origin_postal_code
* origin_country
* destination_name
* destination_address_line_1
* destination_address_line_2
* destination_city
* destination_state
* destination_postal_code
* destination_country
* charges_total
* charges_currency
* actual_shipped_at
* quantity
* weight
* status
* ship_ref
* po
* pickup_reference
* pro_number
* customer_name
* customer_external_id
* customer_account_number
* charges_customer_total
* created_at
* updated_at
* reference_x_label (x is 1-4)
* reference_x_value (x is 1-4)
* tms_created_at - legacy, don't provide if you set created_at
* tms_updated_at - legacy, don't provide if you set updated_at

## Shipments

We recommend that you send an email with Shipment details three
times a day at 6am, 10am and 1pm. We ask that you pick a random number
of minutes or before the hour to help us spread processing a bit. You
may send Shipments as infrequently as once per day, but it will
increase the likelihood of a necessary shipment not being in the system.

**This CSV import depends on customers imported from the Load CSV
file**

The subject of the email must exactly match `tms_shipment:<<API TOKEN>>`

* **external_id**
* **load_external_ids** - comma-separated list of load external_ids
* **customer_external_id** - this should match what is in the load CSV
* customer_name
* customer_account_number
* origin_name
* origin_address_line_1
* origin_address_line_2
* origin_city
* origin_state
* origin_postal_code
* origin_country
* destination_name
* destination_address_line_1
* destination_address_line_2
* destination_city
* destination_state
* destination_postal_code
* destination_country
* charges_currency
* charges_total
* charges_detention
* charges_fuel
* charges_line_haul
* charges_other
* target_ship_start
* target_ship_end
* target_delivery_start
* target_delivery_end
* actual_shipped_at
* actual_delivered_at
* quantity
* weight
* status
* ship_ref
* po
* pickup_reference
* reference_x_label (x is 1-6)
* reference_x_value (x is 1-6)
* pick_reference_x_label (x is 1-6)
* pick_reference_x_value (x is 1-6)
* delivery_reference_x_label (x is 1-6)
* delivery_reference_x_value (x is 1-6)
* created_at
* updated_at

## Carriers

We recommend that you send an email with Carriers once a day. If you
can detect changed records, we recommend only sending changes.

The subject of the email must exactly match `tms_carrier:<<API TOKEN>>`

* **external_id**
* **name**
* inactive - true or false
* address_name
* address_line_1
* address_line_2
* city
* state
* postal_code
* country
* mc_number
* scac
* ein
* dot_number
* **remit_to_name**
* remit_to_address_line_1
* remit_to_address_line_2
* remit_to_city
* remit_to_state
* remit_to_postal_code
* remit_to_country

## Invoice Approval Response

This document will be sent each time invoices are approved. Since each
invoice may contain many documents, we will repeat the document columns,
incrementing the number (displayed as "X" below) for each record.

If you are receiving updates via email they will be sent to an email
address you provide us and will be sent with the subject line "Invoice
approved"

In some special situations (specific customers) `_url_` may be changed
to `_filename_` and some of the document fields may be removed. If you
are one of these special cases you'll know it.

* load_id
* load_external_id
* invoice_number
* invoice_date
* invoice_pay_on
* invoice_total
* invoice_quickpay
* invoice_approver
* carrier_external_id
* remit_to_name
* remit_to_address_line_1
* remit_to_address_line_2
* remit_to_city
* remit_to_state
* remit_to_postal_code
* remit_to_country
* document_type_X
* document_proof_of_delivery_X
* document_visibility_X
* document_url_X
* combined_document_type_X
* combined_document_proof_of_delivery_X
* combined_document_visibility_X
* combined_document_url_X
* combined_customer_documents_url
* combined_carrier_documents_url

