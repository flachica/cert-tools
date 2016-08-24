# cert-tools
Command line tools for designing certificate templates, instantiating a certificate batch, and import/export tasks

see example of certificate template and batch creation in sample_data 

# scripts

## create_certificate_template.py

create a certificate template based on config.py. You can specify additional global fields (fields that apply for every certificate in the batch) and additional per-recipient fields (fields that you will specify per-recipient).

additional_global_fields:  For each additional global field, you must indicate:

- the jsonpath to the field
- the global value to use

Example:
    additional_global_fields = [
        {
            'path': '$.certificate.subtitle',
            'value': 'kim custom subtitle',
        }
   ]

additional_per_recipient_fields: Although these are per-recipient, we still use these for the template creation process. Except in this case, we apply merge tags as values. This is to allow additional tooling to replace instantiate_certificate_batch.py. For each additional per-recipient field, you must indicate:

- the jsonpath to the field
- the merge_tag placeholder to use
- the csv column where the value (per recipient) can be found. This is used by instantiate_certificate_batch

Example:
    additional_per_recipient_fields = [
        {
            'path': '$.assertion.evidence',
            'value': '*|EVIDENCE|*',
            'csv_column': 'jobTitle'
        }
   ]
   
   
## instantiate_certificate_batch.py

This script takes the template generated in the previous step and csv file (configurable, by default in rosters) as input. It generates a certificate per recipient based on the values in the csv file.

The csv file must always contain:

- familyName
- givenName
- pubkey
- identity

It may also contain custom fields per-recipient. In create_certificate_template.py, we populated the template with merge tags for these fields. This script will now populate each recipient's certificate with the value in the corresponding csv file. So in this example, we'll get the value in the csv column 'jobTitle' to populate the 'evidence' field on the certificate's assertion section.

    additional_per_recipient_fields = [
        {
            'path': '$.assertion.evidence',
            'value': '*|EVIDENCE|*',
            'csv_column': 'evidence'
        }
   ]
   
   
