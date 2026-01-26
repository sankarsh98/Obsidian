# Apache Flink

> *Stateful stream processing at scale*

---

## What is Flink?

Apache Flink is a **distributed stream processing framework** known for its low-latency, exactly-once semantics, and powerful state management.

---

## Flink vs Spark Streaming

| Aspect | Flink | Spark Streaming |
|--------|-------|-----------------|
| **Model** | True streaming | Micro-batch |
| **Latency** | Milliseconds | Seconds |
| **State** | First-class | Limited |
| **Exactly-once** | Native | With Kafka |
| **Complexity** | Higher | Lower |

---

## Core Concepts

### DataStream API

```java
StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();

DataStream<Event> events = env
    .addSource(new FlinkKafkaConsumer<>("topic", schema, properties))
    .filter(event -> event.getType().equals("click"))
    .keyBy(Event::getUserId)
    .window(TumblingEventTimeWindows.of(Time.minutes(5)))
    .reduce((e1, e2) -> e1.merge(e2));

events.addSink(new FlinkKafkaProducer<>("output-topic", schema, properties));
env.execute("Click Aggregation");
```

### Windowing

| Window Type | Description |
|-------------|-------------|
| **Tumbling** | Fixed, non-overlapping |
| **Sliding** | Overlapping windows |
| **Session** | Gap-based grouping |
| **Global** | All elements in one |

### State Management

```java
// Keyed state example
ValueState<Long> countState = getRuntimeContext().getState(
    new ValueStateDescriptor<>("count", Long.class)
);

countState.update(countState.value() + 1);
```

---

## When to Use Flink

- **Ultra-low latency** requirements
- **Complex event processing** (CEP)
- **Stateful streaming** applications
- **Exactly-once** guarantees critical

---

*Back to: [[Index|Tools Home]]*
