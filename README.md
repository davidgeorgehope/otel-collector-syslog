# Syslog to Elastic via OpenTelemetry Collector

This guide provides instructions for configuring the OpenTelemetry Collector (OTel Collector) to receive syslog data and send it to Elastic.

## Configuration Overview

1. **Rsyslog** captures syslog messages and forwards them to the OTel Collector using a specified format and protocol.
2. **OTel Collector** receives syslog data, processes it, and sends it to Elastic.

## Files

- `config.yaml`: This is the configuration file for the OTel Collector.
- `rsyslog.conf` (or wherever you've stored the rsyslog configuration): Contains the rsyslog settings for forwarding syslog messages to the OTel Collector.

## Setting Up OTel Collector

### Configuration Details

Here are some highlights of the `config.yaml` file:

- **Receivers**:
  - `otlp`: Receives traces and metrics data using the OTLP protocol.
  - `syslog`: Receives syslog data on TCP at `0.0.0.0:54527` using the `rfc3164` protocol. Also includes an operator to move the message attributes to the body.

- **Processors**:
  - `batch`: Batches the data before exporting.

- **Exporters**:
  - `logging`: Logs the received data in detail.
  - `otlp/elastic`: Sends data to the provided Elastic endpoint. Remember to keep your Authorization header value confidential.

### Starting the OTel Collector

Ensure that you have the OTel Collector installed. Use the provided `config.yaml` to start the OTel Collector:

```bash
otelcol --config config.yaml
```

## Setting Up Rsyslog

Update your rsyslog configuration file (`rsyslog.conf` or another specified file) with the provided configuration:

This configuration:

- Uses the `UTCTraditionalForwardFormat` template for syslog messages.
- Forwards all messages to `0.0.0.0` on port `54527` via TCP.

After updating the configuration, restart rsyslog:

```bash
sudo systemctl restart rsyslog
```

## Verify Data Flow

Once both rsyslog and the OTel Collector are running:

1. Generate some syslog messages on your system.
2. Verify that the messages are received and processed by the OTel Collector through its logs.
3. Check your Elastic instance to see the received syslog data.

## Security Note

Remember to keep the Authorization header in the OTel Collector configuration secure. Avoid exposing sensitive tokens in version control or publicly accessible areas.
