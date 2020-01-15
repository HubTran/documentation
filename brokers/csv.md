# CSV via SFTP

All files should be sent in CSV format.

The names should match what is being uploaded, carriers.csv, loads.csv,
shipments.csv, stops.csv, customer_invoices.csv, and customers.csv.
You should add ".inprogress" to the end of
the filename while the file is being imported so we don't attempt to
import partially uploaded files.

All carriers must be uploaded every hour.

The last 24 hours of updated+inserted loads must be uploaded at most every hour
between 6 AM and 6 PM and the last 60 days of updated+inserted loads must
be uploaded at 10 PM every night. This may be subject to change
depending on how large the files are.

All dates + times should be in
[iso8601](https://en.wikipedia.org/wiki/ISO_8601) format.

Required fields are **bold**.

* [Carriers](#carriers)
* [Loads](#loads)
* [Shipments](#shipments)
* [Stops](#stops)
* [Invoice Approval Response](#invoice-approval-response)
* [Customer Invoices](#customer-invoices)
* [Customers](#customers)

## Carriers

* **external_id**
* **name**
* inactive - recommended, true or false
* address_name - recommended
* address_line_1 - recommended
* address_line_2 - recommended
* city - recommended
* state - recommended
* postal_code - recommended
* country - recommended
* mc_number - recommended
* scac - recommended
* ein
* dot_number
* **remit_to_name**
* remit_to_address_line_1 - recommended
* remit_to_address_line_2 - recommended
* remit_to_city - recommended
* remit_to_state - recommended
* remit_to_postal_code - recommended
* remit_to_country - recommended

## Loads

* **load_id** - Shows up in the UI, needs to be unique
* **external_id** - needs to be unique
* **carrier_external_id**
* carrier_name - recommended
* carrier_scac
* origin_name - recommended
* origin_address_line_1 - recommended
* origin_address_line_2 - recommended
* origin_city - recommended
* origin_state - recommended
* origin_postal_code - recommended
* origin_country - recommended
* destination_name - recommended
* destination_address_line_1 - recommended
* destination_address_line_2 - recommended
* destination_city - recommended
* destination_state - recommended
* destination_postal_code - recommended
* destination_country - recommended
* charges_total - recommended
* charges_currency - recommended
* actual_shipped_at - recommended
* quantity
* weight
* status
* ship_ref
* po
* pickup_reference
* pro_number
* customer_name - recommended
* customer_external_id - recommended
* customer_account_number - recommended
* charges_customer_total - recommended
* created_at
* updated_at
* reference_x_label (x is 1-6) - recommended
* reference_x_value (x is 1-6) - recommended
* line_item_x_description (x is 1-10)
* line_item_x_type_code (x is 1-10)
* line_item_x_total (x is 1-10)
* line_item_x_customer_total (x is 1-10)
* line_item_x_carrier_external_id (x is 1-10; value is same as external_id in the carriers file)
* tms_created_at - legacy, don't provide if you set created_at
* tms_updated_at - legacy, don't provide if you set updated_at

## Shipments

**This CSV import depends on customers imported from the Load CSV
file**

* **external_id**
* **load_external_ids** - comma-separated list of load external_ids
* **customer_external_id** - this should match what is in the load CSV
* customer_name - recommended
* customer_account_number - recommended
* origin_name - recommended
* origin_address_line_1 - recommended
* origin_address_line_2 - recommended
* origin_city - recommended
* origin_state - recommended
* origin_postal_code - recommended
* origin_country - recommended
* destination_name - recommended
* destination_address_line_1 - recommended
* destination_address_line_2 - recommended
* destination_city - recommended
* destination_state - recommended
* destination_postal_code - recommended
* destination_country - recommended
* charges_currency - recommended
* charges_total - recommended
* charges_detention - recommended
* charges_fuel - recommended
* charges_line_haul - recommended
* charges_other
* target_ship_start
* target_ship_end
* target_delivery_start
* target_delivery_end
* actual_shipped_at - recommended
* actual_delivered_at - recommended
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

## Stops

* **external_id**
* **load_external_id** - must match what is in the loads file
* shipment_external_ids - recommended, comma-separated list of shipment external
  ids, must match what is in the shipments file
* **type** - the value should be "pick" or "drop"
* name - recommended
* address_line_1 - recommended
* address_line_2 - recommended
* city - recommended
* state - recommended
* postal_code - recommended
* country

## Invoice Approval Response

This document will be sent each time invoices are approved. Since each
invoice may contain many documents, we will repeat the document columns,
incrementing the number (displayed as "X" below) for each record.

In some special situations (specific customers) `_url_` may be changed
to `_filename_` and some of the document fields may be removed. If you
are one of these special cases you'll know it.

Once the invoice has been inserted into your TMS you should send a GET
request to the url listed in `acknowledgement_url` to verify the invoice
in HubTran.

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
* acknowledgement_url

## Customer Invoices

* **external_id**
* **number**
* **date**
* **total**
* terms
* **customer_external_id** - same thing as the external_id in the customers file
* **shipment_external_ids** - comma-separated list of shipment external_ids
* ship_date
* delivery_date
* distance
* distance_unit
* weight
* weight_unit
* bill_to_name
* bill_to_address_line_1
* bill_to_address_line_2
* bill_to_city
* bill_to_state
* bill_to_postal_code
* bill_to_country
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
* line_item_x_type_code
* line_item_x_rate
* line_item_x_description
* line_item_x_total
* reference_x_label
* reference_x_value

## Customers

* **external_id**
* **name**
* account_number
* invoicing_interval - "daily", "weekly", "monthly", or "never", default is "daily"
* invoicing_method - "print" or "email", default is "email"
* invoicing_days_to_pay
* invoicing_billing_email
* invoicing_email_link_or_attachment - "link" or "attachment", default is "link"
