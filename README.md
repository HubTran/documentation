# API Documentation

You might want to take a look at the [high level overview](high_level_overview.pdf) before digging into the more detailed documentation.

## Brokers

* [REST](brokers/rest.md)
* [Webhook](brokers/webhook.md)
* [CSV](brokers/csv.md)

## Factors

* [REST](factors/rest.md)
* [Webhook](factors/webhook.md)

## General Notes

In HubTran's terminology, a "load" represents the physical truck move and a "shipment" represents the unit of cargo that is picked up and
delivered. A load may have one shipment or multiple shipments. A shipment may move on one load or multiple loads. A load is billed on the carrier invoice. A shipment is billed on the broker invoice to its customer. If your TMS is not structured around the concept of loads and shipments, HubTran can map to your structure.

You'll also see references to "external_id". This is our way of linking objects in HubTran with objects in your TMS so it should be YOUR internal id for the object. It should be unique and immutable.

At various times we'll add more keys to our webhook payloads that might not be documented. Your implementation should be able to handle us adding keys without notice. However, we'll never remove a key from the payload without giving you plenty of notice.

## Document Types

We've also created a [document types endpoint](./brokers/rest.md#document-types) which returns all the
document type options as well as the ones currently enabled for your
account. These will help you when you are mapping our document types
in our webhook to your document storage types.
