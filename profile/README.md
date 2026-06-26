<div align="center">

# 🌀 BabelQueue

### Polyglot Queues, Simplified.

**One strict JSON message envelope that Laravel, Symfony, Go, Python, Java, .NET and
Node.js all speak — over the broker you already run.**

[![Website](https://img.shields.io/badge/website-babelqueue.com-10B981)](https://babelqueue.com)
[![License: MIT](https://img.shields.io/badge/license-MIT-blue.svg)](#-license)
![Status](https://img.shields.io/badge/status-1.x_GA-10B981)
![Brokers](https://img.shields.io/badge/brokers-7-10B981)

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
- 🔌 **Broker-agnostic** — Redis, RabbitMQ, Amazon SQS, Azure Service Bus, Apache Pulsar,
  Apache Kafka & ActiveMQ/Artemis — swap by a config line.
- 🪶 **Drop-in & lightweight** — reuse your framework's worker; zero heavy dependencies.

## Ecosystem

Each language ships a framework-agnostic **core** plus thin **framework adapters**,
so the wire format stays identical everywhere.

| Language | Core | Framework adapters + broker transports | Registry | Status |
| :--- | :--- | :--- | :--- | :--- |
| PHP | `babelqueue/php-sdk` | `babelqueue/laravel`, `babelqueue/symfony` (Redis/AMQP/SQS/Pulsar/Kafka/Artemis transports in the core) | Packagist | ✅ 1.x |
| Python | `babelqueue` | `[celery]` `[django]` + broker extras `[redis]` `[amqp]` `[sqs]` `[pulsar]` `[kafka]` `[azureservicebus]` `[artemis]` | PyPI | ✅ 1.x |
| Go | `babelqueue-go` | `…/redis` `…/amqp` `…/sqs` `…/pulsar` `…/kafka` `…/artemis` `…/azureservicebus` + `…/asynq` `…/machinery` | Go modules | ✅ 1.x |
| Node.js | `@babelqueue/core` | `@babelqueue/bullmq` `@babelqueue/nestjs` + `redis` `rabbitmq` `sqs` `pulsar` `kafka` `artemis` `azure-service-bus` packages | npm | ✅ 1.x |
| Java | `com.babelqueue:babelqueue-core` | `…:babelqueue-spring` + `-redis` `-sqs` `-pulsar` `-kafka` `-artemis` `-azureservicebus` | Maven Central | ✅ 1.x |
| .NET | `BabelQueue.Core` | `BabelQueue.MassTransit` + `.Redis` `.Sqs` `.Pulsar` `.Kafka` `.Artemis` `.AzureServiceBus` | NuGet | ✅ 1.x |

**Brokers — every SDK speaks the same envelope over each:** Redis · RabbitMQ · Amazon SQS ·
Azure Service Bus · Apache Pulsar · Apache Kafka · ActiveMQ/Artemis. Each is locked by the
cross-SDK conformance suite and proven by a live cross-language interop demo. _(PHP × Azure
Service Bus is the one documented deferral — every other (language × broker) cell ships.)_

## Get involved

- 🌐 **Website & docs:** [babelqueue.com](https://babelqueue.com)
- 🤝 **Contributing:** see [CONTRIBUTING](https://github.com/BabelQueue/.github/blob/main/CONTRIBUTING.md)
- 🔒 **Security:** see [SECURITY](https://github.com/BabelQueue/.github/blob/main/SECURITY.md)
- ❤️ **Sponsor:** [github.com/sponsors/muhammetsafak](https://github.com/sponsors/muhammetsafak)
- 💬 Open an issue or discussion on the relevant repository.

> **Status:** BabelQueue is **GA (1.x)** — every SDK is published with a SemVer-stable
> public API (breaking changes require a MAJOR, per the deprecation policy), and each
> **produces and consumes** the envelope across the seven brokers above. The wire
> contract is frozen at `schema_version: 1`.

## 📄 License

Open source under the **MIT License** © Muhammet Şafak.
