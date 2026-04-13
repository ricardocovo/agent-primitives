---
description: "Generate P1-5 events.yaml — event catalog documenting all published and consumed events/messages with schemas, topics, routing keys, and ordering guarantees. Wave 3 artifact. Applies to service and pipeline repos using async messaging."
tools: [read, search, execute]
---

Generate **events.yaml** (P1-5) for the repository at `$input`.

## Applicability

This artifact applies to repos that publish or consume asynchronous events/messages. Skip for repos with no message broker integration (no Kafka, RabbitMQ, Azure Service Bus, SNS/SQS, EventGrid, etc.).

## What to Analyze

1. **Event publishers**: Producer classes, `publish()`/`send()` calls, event bus integrations
2. **Event consumers**: Subscriber classes, message handlers, `@KafkaListener`, `@EventHandler`, queue triggers
3. **Event schemas**: Event classes/DTOs, Avro schemas, Protobuf definitions, JSON schema files
4. **Topics/queues**: Topic names, queue names, routing keys, exchange configurations
5. **Broker configuration**: Connection configs, consumer group names, retry policies, DLQ settings
6. **Ordering guarantees**: Partition keys, sequence numbers, FIFO queue settings

## Output

Write to `architects-metadata/phase1/{repo-name}/events.yaml`

```yaml
artifact: events
version: "1.0"
generated: "{ISO-8601 date}"
repo-name: "{name}"

broker-type: "{Kafka|RabbitMQ|Azure-Service-Bus|SNS-SQS|EventGrid|mixed}"

published-events:
  - event-name: "{name}"
    topic: "{topic or exchange}"
    schema-format: "{JSON|Avro|Protobuf|CloudEvents}"
    key-fields: ["{field used for partitioning}"]
    description: "{what triggers this event}"
    payload-fields:
      - name: "{field}"
        type: "{type}"
        required: true|false

consumed-events:
  - event-name: "{name}"
    topic: "{topic or queue}"
    consumer-group: "{group}"
    handler: "{class.method}"
    idempotent: true|false
    retry-policy: "{policy description}"
    dlq: "{DLQ topic/queue or null}"
    description: "{what this handler does}"
```

## Validation

- Every producer/publisher found in code must have a corresponding `published-events` entry
- Every consumer/handler found in code must have a corresponding `consumed-events` entry
- Topic/queue names must match configuration files
