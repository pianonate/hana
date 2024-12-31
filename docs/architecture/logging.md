# Logging Framework

## Overview
The logging framework provides structured logging using the `tracing` crate across Hana's distributed system. It focuses on clear, actionable log levels that help diagnose issues and monitor system health.

## Log Levels

### ERROR
Used for failures requiring immediate attention:
- Plugin crashes
- Network connection failures
- Resource exhaustion
- State synchronization failures
- Configuration errors preventing startup

### WARN
Used for recoverable issues affecting performance or reliability:
- Network latency spikes
- Frame rate drops
- Failed connection retries
- Plugin load warnings
- Resource usage approaching limits

### INFO
Used for significant state changes and normal operations:
- System startup/shutdown
- Plugin load/unload
- Display configuration changes
- Network peer connections/disconnections
- Controller role changes

### DEBUG
Used for detailed troubleshooting information:
- Message routing details
- Frame timing breakdowns
- Resource allocation events
- Configuration loading steps
- State synchronization details

### TRACE
Used only for very detailed debugging:
- Individual frame events
- Network message contents
- Resource usage metrics
- State change details
- Parameter update events

## Library vs Application Logging

### Library Logging
- Use structured logging with clear targets
- Focus on machine-readable data
- Keep performance impact minimal
- Avoid expensive string formatting

Example:
```rust
use tracing::{debug, info, warn};

#[instrument]
fn process_message(msg: &Message) {
    debug!(target: "network", message_type = ?msg.kind, "Processing message");
    
    if msg.size > MAX_SIZE {
        warn!(target: "network", size = msg.size, "Message exceeds size limit");
        return;
    }
    
    info!(target: "network", peer = ?msg.sender, "Message processed");
}
```

### Application Logging
- Format logs for human readability
- Aggregate logs from all nodes
- Include contextual information
- Enable filtering and search

## Best Practices

1. Use consistent log targets matching module names
2. Include relevant context in structured fields
3. Log at appropriate levels based on operational impact
4. Avoid logging sensitive information
5. Consider performance impact of debug/trace logging

## Configuration
```toml
[dependencies]
tracing = "0.1"
tracing-subscriber = "0.3"
```

Basic setup:
```rust
use tracing_subscriber::{prelude::*, fmt, EnvFilter};

fn setup_logging() {
    let filter = EnvFilter::try_from_default_env()
        .unwrap_or_else(|_| EnvFilter::new("info"));
        
    tracing_subscriber::registry()
        .with(fmt::layer())
        .with(filter)
        .init();
}
```

## Doc Links
- [Architecture](../architecture/README.md) - High level system design
- [Developer](../developer/README.md) - Coding guidelines for hana contributors
- [Overview](../../README.md) - Hana overview
- [Plugin Development](../plugins/README.md) - Guidelines for plugin development
- [User](../user/README.md) - Hana user documentation