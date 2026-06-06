# Contributing to BabelQueue

Thanks for your interest in BabelQueue — *Polyglot Queues, Simplified.* These
guidelines apply org-wide; an individual repository may add its own `CONTRIBUTING.md`
that takes precedence.

## The one rule that matters most

**BabelQueue's whole value is that every language produces and consumes the
*identical* wire envelope.** So the message envelope is a **frozen contract**
(`schema_version: 1`):

```json
{ "job": "urn:babel:<context>:<event>", "trace_id": "<uuid>", "data": { … },
  "meta": { "id": "<uuid>", "queue": "…", "lang": "…", "schema_version": 1, "created_at": <unix-ms> },
  "attempts": 0 }
```

- **Never** add, rename, remove or retype an envelope field — across any SDK —
  without an accepted **ADR** and a `schema_version` decision.
- The identity is the **URN** in `job` (never a class/type name); `urn` is accepted
  only as an inbound alias.
- `data` is **pure JSON**. Never use a language's native serialization across the wire.
- **Preserve and forward `trace_id`** unchanged on every hop.
- Forbidden legacy keys: `timestamp`, `meta.max_retries`, `meta.source`, `meta.ts`.

If a change would alter the bytes on the wire, it is an architectural decision —
open an issue/discussion first.

## How to contribute

1. **Open an issue first** for anything non-trivial (bug, feature, contract
   question) so we can agree on direction before code.
2. **Fork & branch** from `main` (e.g. `fix/redis-reserve`, `feat/symfony-serializer`).
3. **Write tests** with every change. A PR without tests will be asked for them.
4. **Keep cross-SDK parity.** A behavior added to one SDK should be expressible in
   the others; shared golden fixtures define conformance.
5. **Open a PR** using the template; make sure **CI is green**.

## Development setup

| Repo | Setup | Test |
| :--- | :--- | :--- |
| `php-sdk`, `laravel` (PHP) | `composer install` | `vendor/bin/phpunit` |
| `babelqueue.com` (site) | `npm install` | `npm run build` |
| Future SDKs | per that repo's README | per that repo's README |

The Laravel adapter resolves the core (`babelqueue/php-sdk`) via a local Composer
`path` repository when both are checked out side by side.

## Coding standards

- **PHP:** PSR-12, `declare(strict_types=1)`, typed signatures, meaningful docblocks.
- **Commits:** [Conventional Commits](https://www.conventionalcommits.org/)
  (`feat:`, `fix:`, `docs:`, `refactor:`, `test:`, `chore:`).
- **Changelog:** user-facing changes get a `CHANGELOG.md` entry (Keep a Changelog).
- **Versioning:** [SemVer](https://semver.org/). The package version and the envelope
  `schema_version` are independent axes.
- **CI/CD:** every repo ships GitHub Actions for tests (on PR/push) and publish
  (on tag). A red pipeline blocks merge.

## Definition of done

- [ ] Tests added/updated and passing; CI green.
- [ ] No change to the frozen envelope without an ADR + `schema_version` decision.
- [ ] Cross-SDK parity preserved (or explicitly scoped).
- [ ] Docs / changelog updated where user-facing.
- [ ] Conventional-commit messages.

## License

By contributing, you agree that your contributions are licensed under the
project's **MIT License**.

Questions? Open a discussion, or email **info@muhammetsafak.com.tr**.
