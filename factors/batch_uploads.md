# Batch Upload API

## Overview

Uploading a batch to HubTran is a two step process:

1. Upload document files via the upload file API endpoint, 1 at a time, saving the returned HubTran ID for each upload
2. Call the invoice batch API endpoint with all the batch and invoice data, attaching the uploaded files using the ID from the upload file endpoint


## Authentication

Uses HubTran's [standard token authentication](https://github.com/HubTran/documentation/blob/master/rest_authentication.md).

## Upload File Endpoint

### Request

POST https://api.hubtran.com/factor/uploaded_files

Upload using multipart/form-data. Example CURL call:

```
curl -H "Authorization: Token token=YOUR_TOKEN" -F file=@filename https://api.hubtran.com/factor/uploaded_files
```

If the doctype is known, you can set it by passing up a document_type param:

```
curl -H "Authorization: Token token=YOUR_TOKEN" -F file=@filename https://api.hubtran.com/factor/uploaded_files?document_type=billOfLading
```

If the document type is unknown, or it's a billing file, omit the document type.

Allowed document types are:

- billOfLading
- proofOfDelivery
- invoice
- laborReceipt
- factorInvoice
- noticeOfAssignment
- noticeOfRelease
- rateConfirmation
- weightTicket

If there's a document type you use that's not on this list, let us know.

### Response

JSON:

```
{
    "id": "123"
}
```

Note: `uploaded_file_id` might be an integer or alphanumeric. Best to treat it as a string. Store this ID for use in the batch endpoint

## Invoice Batch Endpoint

### Request

POST https://api.hubtran.com/factor/invoice_batches

JSON:

```
{
    "batch": {
        "carrier": { "external_id": "id" }, // Required. This is the ClientKey from Cadence.
        "external_id": "batch-id"  // Required. This is the internal ID used by Fleet Docs for the batch.
        "account_payments": [ // Optional. The ordering of these is preserved
            {
                "external_id": "some-id", // Required. This is the CliBankKey from Cadence
                "amount": 700.0 // Required.
            },
            {
                "external_id": "some-id-2", // This is the CliBankKey from Cadence
                "amount": 300.0
            }
        ],
        "uploaded_files": [ // Required if invoice level files are not used
            { 
                "id": "upload-1", // Required, this is the ID from the uploaded files endpoint
            },
            {
                "id": "upload-2"
            }
        ]
    },
    "invoices": [
        {
            "external_id": "invoice-id", // Required. This should be a unique key for this batch,
                                         // and it can only be submitted once
            "debtor": { "external_id": "id" }, // Optional.
            "number": "12345", // Required.
            "amount": 250.25, // Required.
            "date": "2019-03-22", // Optional. iso8601 format
            "load_id": "L123-ASD", // Required.

            "uploaded_files": [ // Required if batch level files are not uploaded
                { 
                    "id": "upload-1", // Required, this is the ID from the uploaded files endpoint
                },
                {
                    "id": "upload-2"
                }
            ]
        },
        {
            // second invoice
        }
    ]
}
```

- `batch.external_id` is an ID provided by you to uniquely identify this batch. If the same external_id is submitted twice, the second will fail
- `invoices[].external_id` is similar to the batch ID, it's an ID from you that uniquely identifies that invoice. If it's submitted more than once, the whole batch will fail.
- You can either send up `batch.uploaded_files` OR `invoices[].uploaded_files`. Sending up both will result in an error.


### Response

If there are any errors, you'll receive something like this:

```
{ "errors":
    [
        {
            "message": "batch/carrier is missing"
        }
    ]
}
```

On success, you'll receive a 201 response code.

