# solink-tracing-flat-json

This is an open source library that can be used with tracing to log as flattened JSON:

```rs
    use solink_tracing_flat_json::SolinkJsonFormat;

    let log_to_file = tracing_subscriber::fmt::layer()
        .event_format(
            SolinkJsonFormat::new()
        )
        .fmt_fields(JsonFields::default());
    tracing_subscriber::registry().with(log_to_file).init();
```

This will serialize a timestamp, all variables in the event, the name of the current span, and all variables in the current and all parent spans. Something like this:

```rs
#[tokio::main(flavor = "current_thread")]
async fn main() {
    a(42).await;
}


#[instrument]
async fn a(x: u64) {
    b(x).await;
}

#[instrument]
async fn b(y: u64) {
    info!(z = 94, "Hello from b")
}
```

Will produce output like:

```txt
{"timestamp":"2024-06-18T21:17:44.902137000Z","level":"INFO","message":"Hello from b","z":94,"span":"b","y":42,"x":42}
```
