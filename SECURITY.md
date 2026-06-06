# Security Policy

## Supported versions

BabelQueue is **pre-1.0**. Until a `1.0` release, security fixes are applied to the
latest version of each package only.

| Package family | Supported |
| :--- | :--- |
| Latest release / `main` | ✅ |
| Older pre-1.0 tags | ❌ |

## Reporting a vulnerability

**Please do not open a public issue for security vulnerabilities.**

Report privately, either:

- via **GitHub Private Vulnerability Reporting** — "Report a vulnerability" on the
  affected repository's **Security** tab, or
- by email to **info@muhammetsafak.com.tr** with subject `BabelQueue Security`.

Please include: the affected package and version, the broker (Redis/RabbitMQ), a
description, and a reproduction or proof-of-concept if possible.

We aim to acknowledge within **72 hours**, agree on a disclosure timeline, and
credit reporters who wish to be named once a fix is released.

## Scope & posture

BabelQueue is a queue serialization/routing layer. Some properties to keep in mind
when assessing reports:

- **No code deserialization.** Messages are plain JSON envelopes — BabelQueue never
  uses PHP `serialize()`/`unserialize()` (or equivalent) for cross-language payloads.
- **Payloads are untrusted input.** Consumers must validate `data`; a URN is routing
  identity, **not** authorization.
- **Credentials** (Redis/RabbitMQ) live in the host app's configuration/secrets and
  never travel in the envelope.

In scope: the BabelQueue SDKs and their handling of messages, brokers and
configuration. Out of scope: vulnerabilities in third-party brokers or drivers
themselves (report those upstream), and issues requiring a compromised host.
