# classroom-sidecar

A lightweight **sidecar system on AWS** that fills operational gaps for small franchise classrooms — without modifying the headquarters (HQ) system.

> Status: early development

## Why a sidecar?

Franchise classrooms run on an HQ-provided core system. It covers the standard workflow well, but some day-to-day needs of an individual classroom are out of its scope (for example, flexible notifications to parents).

Instead of changing or scraping the HQ system, this project runs **next to it**, like a motorcycle sidecar:

- The HQ system stays the system of record for everything it already covers.
- The sidecar only handles the gaps, with the minimum data it needs.
- Data moves between them only through methods the HQ officially allows (e.g. manual export/import), never by automated login or scraping.

This is the same pattern used in large enterprises, where a core ERP is complemented by small surrounding systems instead of being customized.

## Planned features

- [ ] Student and guardian contact list (minimum fields only)
- [ ] Email notifications to guardians (announcements, schedule changes)
- [ ] Simple admin screen for the classroom owner (non-technical user)
- [ ] Daily backup and restore procedure

## Architecture (initial)

```
 Classroom owner ──HTTPS──▶  Amazon Lightsail instance
                               ├─ Web app (admin screen)
                               ├─ Database (student / guardian contacts)
                               └─ Mail sending  ──▶ Guardians
                                     │
                     Daily DB dump ──▶ Lightsail bucket (encrypted)
```

Details will be updated as the implementation progresses.

## Design principles

The system handles personal data of children and their guardians, so the non-functional requirements drive the design.

| Area | Approach |
| --- | --- |
| Security | HTTPS only; only port 443 open to the internet; SSH restricted; least-privilege IAM; MFA on the AWS account and admin login |
| Privacy | Store only the minimum fields needed to send notifications; obtain guardians' consent for the purpose of use |
| Data at rest | Lightsail disks and snapshots are encrypted at rest by default |
| Availability | Daily automatic snapshots (7-day retention) plus monthly manual snapshots for longer retention |
| Recoverability | Daily database dump to a separate bucket; restore procedure tested periodically |
| Cost | Single small Lightsail instance sized for one classroom; no always-on extra services |
| Operability | Admin screen designed for a non-technical owner |

## Configuration

Copy the template and fill in your own values. **Never commit the real `.env`.**

```
cp .env.example .env
```

Secrets (passwords, API keys, credentials) belong in `.env` on the server or in AWS Systems Manager Parameter Store — not in this repository.

## What is NOT in this repository

This is a public repository. The following are intentionally kept out:

- Any student or guardian data (real or exported)
- Credentials, keys, and passwords
- Environment-specific values (domain, allowed IP addresses, account IDs, bucket names)
- Operational runbooks for the production environment

Sample data in this repository is always fictitious.

## Disclaimer

This is an independent personal project. It is not affiliated with, endorsed by, or connected to any franchise headquarters or brand.

## License

MIT (see [LICENSE](LICENSE))
