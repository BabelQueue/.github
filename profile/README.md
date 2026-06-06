<div align="center">

# 🌀 BabelQueue

### Polyglot Queues, Simplified.

**One strict JSON message envelope that Laravel, Symfony, Go, Python, Java, .NET and
Node.js all speak — over the broker you already run.**

[![Website](https://img.shields.io/badge/website-babelqueue.com-10B981)](https://babelqueue.com)
[![License: MIT](https://img.shields.io/badge/license-MIT-blue.svg)](#-license)
![Status](https://img.shields.io/badge/status-pre--1.0-orange)

</div>

---

## The problem

Frameworks like Laravel serialize queued jobs with PHP's `serialize()` — a PHP-only
object graph. The moment a Go, Python or Java service needs to consume that job, it
can't read it. The usual escapes — adopt Kafka/gRPC, or hand-roll bridge services —
are heavy and invasive.

## The idea

**Don't change your broker. Change only the serialization.**

BabelQueue replaces `serialize()` with a frozen, language-neutral JSON **envelope**
and routes by a **URN** instead of a class name — so a job produced in one language
is consumed natively in another, with a built-in `trace_id` for end-to-end tracing.
No sidecar, no proxy, no broker plugin.

```json
{
  "job": "urn:babel:orders:created",
  "trace_id": "7b3f9c2a-e41d-4f88-9b2a-1c0d5e6f7a8b",
  "data": { "order_id": 1042, "amount": 99.90 },
  "meta": { "id": "…", "queue": "orders", "lang": "php", "schema_version": 1, "created_at": 1749132727000 },
  "attempts": 0
}
```

## A quick taste — Laravel produces, anything consumes

```php
use BabelQueue\Facades\BabelQueue;

BabelQueue::publish('urn:babel:orders:created', ['order_id' => 1042]);
```

```python
# a Python service consumes the very same message — natively
@bq.handler("urn:babel:orders:created")
def on_order_created(data, meta):
    ...  # AI/ML, data processing, anything
```

## Why teams use it

- 🧩 **Agnostic serialization** — a strict JSON envelope, no `serialize()` lock-in.
- 🔖 **URN routing** — identity (`urn:babel:<context>:<event>`) decoupled from class names.
- 🔭 **Built-in `trace_id`** — follow one message across every service and language.
- 🔌 **Broker-agnostic** — Redis & RabbitMQ, swap by a config line.
- 🪶 **Drop-in & lightweight** — reuse your framework's worker; zero heavy dependencies.

## Ecosystem

Each language ships a framework-agnostic **core** plus thin **framework adapters**,
so the wire format stays identical everywhere.

| Language | Package(s) | Registry | Status |
| :--- | :--- | :--- | :--- |
| PHP — core | `babelqueue/php-sdk` | Packagist | 🚧 Pre-1.0 |
| PHP — Laravel | `babelqueue/laravel` | Packagist | 🚧 Pre-1.0 |
| PHP — Symfony | `babelqueue/symfony` | Packagist | 📋 Planned |
| Go | `babelqueue-go` | Go modules | 📋 Planned |
| Python | `babelqueue` | PyPI | 📋 Planned |
| Java | `com.babelqueue:babelqueue-core` | Maven Central | 📋 Planned |
| .NET | `BabelQueue.Core` | NuGet | 📋 Planned |
| Node.js | `@babelqueue/core` | npm | 📋 Planned |

**Brokers:** Redis · RabbitMQ

## Get involved

- 🌐 **Website & docs:** [babelqueue.com](https://babelqueue.com)
- 🤝 **Contributing:** see [CONTRIBUTING](https://github.com/BabelQueue/.github/blob/main/CONTRIBUTING.md)
- 🔒 **Security:** see [SECURITY](https://github.com/BabelQueue/.github/blob/main/SECURITY.md)
- ❤️ **Sponsor:** [github.com/sponsors/muhammetsafak](https://github.com/sponsors/muhammetsafak)
- 💬 Open an issue or discussion on the relevant repository.

> **Status:** BabelQueue is **pre-1.0** and under active development. The wire
> contract is frozen at `schema_version: 1`; package APIs may still change before
> their `1.0` release.

## 📄 License

Open source under the **MIT License** © Muhammet Şafak.
