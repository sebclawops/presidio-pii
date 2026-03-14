---
name: presidio-pii
version: 1.0.1
description: Local PII protection for OpenClaw agents. Scrub customer data before model use, then restore it before delivery.
---

# Presidio PII Protection

Use this skill when customer data may contain PII.

## Use it for

Scrub data before reasoning when working with:
- customer notes
- CRM or lead data
- estimates
- project records
- emails or documents with names, phones, emails, addresses, account details, vessel names, or similar identifiers

## Do not use it for

- internal ops data with no customer PII
- general system tasks
- product, SOP, or status discussions without customer-identifying details

## Fail-closed rule

If Presidio is unhealthy, stop and tell the owner:

> Cannot query or process customer data because Presidio PII protection is offline. Customer data will not be sent unprotected.

## Workflow

### 1) Check health

```bash
bash SKILL_DIR/scripts/presidio-health.sh
```

Only continue if both services are healthy.

### 2) Scrub raw text

```bash
echo "RAW DATA HERE" | python3 SKILL_DIR/scripts/presidio-scrub.py SESSION_ID
```

Use any unique session ID.

Expected output shape:

```json
{
  "text": "[PERSON_1] at [LOCATION_1], phone [PHONE_NUMBER_1]",
  "pii_found": 3,
  "entity_types": ["PERSON", "LOCATION", "PHONE_NUMBER"],
  "mapping_file": "/path/to/mapping.json",
  "session_id": "SESSION_ID"
}
```

Reason only over the `text` field.

### 3) Restore before delivery

```bash
echo "MODEL RESPONSE WITH TOKENS" | python3 SKILL_DIR/scripts/presidio-restore.py SESSION_ID
```

That restores original values and deletes the mapping file by default.

## What gets scrubbed

- names
- phone numbers
- email addresses
- physical addresses and cities
- credit card numbers
- SSNs
- bank details
- vessel names
- IP addresses
- Sea Cool-style project identifiers when matched by custom recognizers

## Safe to pass through

- product names
- project statuses
- SOP terminology
- scope descriptions
- role names
- dollar amounts without customer-identifying context

## Local files

Custom recognizers live in:

- `configs/recognizers.json`

Scripts live in:

- `scripts/presidio-health.sh`
- `scripts/presidio-scrub.py`
- `scripts/presidio-restore.py`

Keep this skill strict, minimal, and local-first.
