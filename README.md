# presidio-pii

Local PII protection skill for OpenClaw.

## Purpose

Use Microsoft Presidio running locally to scrub customer PII before any model sees it, then restore the real values before delivery.

This skill exists to enforce a fail-closed workflow for customer data.

## What it does

- checks local Presidio health
- anonymizes sensitive text into reversible tokens
- stores a temporary local mapping file
- restores tokens back to original values
- deletes the mapping file on restore by default

## When to use it

Use it before reasoning over customer data from tools or sources like:
- CRM records
- estimates
- customer emails
- project notes
- shared docs
- any raw text with names, phone numbers, emails, addresses, account details, or vessel names

Do not use it for:
- internal ops notes with no customer PII
- generic system administration
- product or SOP discussions without customer-identifying data

## Fail-closed rule

If Presidio is unhealthy, stop. Do not send customer data to any model.

## Files

- `SKILL.md` - runtime instructions for the agent
- `scripts/presidio-health.sh` - health check for analyzer and anonymizer
- `scripts/presidio-scrub.py` - anonymize input text and store token mapping
- `scripts/presidio-restore.py` - restore original values and delete mapping by default
- `configs/recognizers.json` - custom recognizers for service areas, vessel names, and Sea Cool-style project IDs

## Notes

- Mapping files are local only
- Default mapping path is `~/.openclaw/presidio/mappings`
- Restore deletes the mapping unless `--keep` is used
- This skill should stay lean, boring, and trustworthy
