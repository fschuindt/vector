---
description: Batches `log` events to Elasticsearch via the `_bulk` API endpoint.
---

<!--
     THIS FILE IS AUTOGENERATED!

     To make changes please edit the template located at:

     scripts/generate/templates/docs/usage/configuration/sinks/elasticsearch.md.erb
-->

# elasticsearch sink

![][assets.elasticsearch_sink]

{% hint style="warning" %}
The `elasticsearch` sink is in beta. Please see the current
[enhancements][urls.elasticsearch_sink_enhancements] and
[bugs][urls.elasticsearch_sink_bugs] for known issues.
We kindly ask that you [add any missing issues][urls.new_elasticsearch_sink_issue]
as it will help shape the roadmap of this component.
{% endhint %}

The `elasticsearch` sink [batches](#buffers-and-batches) [`log`][docs.data-model.log] events to [Elasticsearch][urls.elasticsearch] via the [`_bulk` API endpoint](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-bulk.html).

## Config File

{% code-tabs %}
{% code-tabs-item title="vector.toml (simple)" %}
```coffeescript
[sinks.my_sink_id]
  type = "elasticsearch" # must be: "elasticsearch"
  inputs = ["my-source-id"]
  host = "http://10.24.32.122:9000"

  # For a complete list of options see the "advanced" tab above.
```
{% endcode-tabs-item %}
{% code-tabs-item title="vector.toml (advanced)" %}
```coffeescript
[sinks.elasticsearch_sink]
  #
  # General
  #

  # The component type
  # 
  # * required
  # * no default
  # * must be: "elasticsearch"
  type = "elasticsearch"

  # A list of upstream source or transform IDs. See Config Composition for more
  # info.
  # 
  # * required
  # * no default
  inputs = ["my-source-id"]

  # The host of your Elasticsearch cluster. This should be the full URL as shown
  # in the example.
  # 
  # * required
  # * no default
  host = "http://10.24.32.122:9000"

  # The `doc_type` for your index data. This is only relevant for Elasticsearch
  # <= 6.X. If you are using >= 7.0 you do not need to set this option since
  # Elasticsearch has removed it.
  # 
  # * optional
  # * default: "_doc"
  doc_type = "_doc"

  # Enables/disables the sink healthcheck upon start.
  # 
  # * optional
  # * default: true
  healthcheck = true

  # Index name to write events to.
  # 
  # * optional
  # * no default
  index = "vector-%Y-%m-%d"
  index = "application-{{ application_id }}-%Y-%m-%d"

  # The provider of the Elasticsearch service.
  # 
  # * optional
  # * default: "default"
  # * enum: "default" or "aws"
  provider = "default"
  provider = "aws"

  # When using the AWS provider, the AWS region of the target Elasticsearch
  # instance.
  # 
  # * optional
  # * no default
  region = "us-east-1"

  #
  # Batching
  #

  # The maximum size of a batch before it is flushed.
  # 
  # * optional
  # * default: 10490000
  # * unit: bytes
  batch_size = 10490000

  # The maximum age of a batch before it is flushed.
  # 
  # * optional
  # * default: 1
  # * unit: seconds
  batch_timeout = 1

  #
  # Requests
  #

  # The window used for the `request_rate_limit_num` option
  # 
  # * optional
  # * default: 1
  # * unit: seconds
  rate_limit_duration = 1

  # The maximum number of requests allowed within the `rate_limit_duration`
  # window.
  # 
  # * optional
  # * default: 5
  rate_limit_num = 5

  # The maximum number of in-flight requests allowed at any given time.
  # 
  # * optional
  # * default: 5
  request_in_flight_limit = 5

  # The maximum time a request can take before being aborted.
  # 
  # * optional
  # * default: 60
  # * unit: seconds
  request_timeout_secs = 60

  # The maximum number of retries to make for failed requests.
  # 
  # * optional
  # * default: 5
  retry_attempts = 5

  # The amount of time to wait before attempting a failed request again.
  # 
  # * optional
  # * default: 5
  # * unit: seconds
  retry_backoff_secs = 5

  #
  # Basic auth
  #

  [sinks.elasticsearch_sink.basic_auth]
    # The basic authentication password.
    # 
    # * required
    # * no default
    password = "password"

    # The basic authentication user name.
    # 
    # * required
    # * no default
    user = "username"

  #
  # Buffer
  #

  [sinks.elasticsearch_sink.buffer]
    # The buffer's type / location. `disk` buffers are persistent and will be
    # retained between restarts.
    # 
    # * optional
    # * default: "memory"
    # * enum: "memory" or "disk"
    type = "memory"
    type = "disk"

    # The behavior when the buffer becomes full.
    # 
    # * optional
    # * default: "block"
    # * enum: "block" or "drop_newest"
    when_full = "block"
    when_full = "drop_newest"

    # The maximum size of the buffer on the disk.
    # 
    # * optional
    # * no default
    # * unit: bytes
    max_size = 104900000

    # The maximum number of events allowed in the buffer.
    # 
    # * optional
    # * default: 500
    # * unit: events
    num_items = 500

  #
  # Headers
  #

  [sinks.elasticsearch_sink.headers]
    # A custom header to be added to each outgoing Elasticsearch request.
    # 
    # * required
    # * no default
    X-Powered-By = "Vector"

  #
  # Query
  #

  [sinks.elasticsearch_sink.query]
    # A custom parameter to be added to each Elasticsearch request.
    # 
    # * required
    # * no default
    X-Powered-By = "Vector"
```
{% endcode-tabs-item %}
{% endcode-tabs %}

## Examples

The `elasticsearch` sink batches [`log`][docs.data-model.log] up to the `batch_size` or `batch_timeout` options. When flushed, Vector will write to [Elasticsearch][urls.elasticsearch] via the [`_bulk` API endpoint](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-bulk.html). The encoding is dictated by the `encoding` option. For example:

```http
POST <host>/_bulk HTTP/1.1
Host: <host>
Content-Type: application/x-ndjson
Content-Length: 654

{ "index" : { "_index" : "<index>" } }
{"timestamp": 1557932537, "message": "GET /roi/evolve/embrace/transparent", "host": "Stracke8362", "process_id": 914, "remote_addr": "30.163.82.140", "response_code": 504, "bytes": 29763} 
{ "index" : { "_index" : "<index>" } }
{"timestamp": 1557933548, "message": "PUT /value-added/b2b", "host": "Wiza2458", "process_id": 775, "remote_addr": "30.163.82.140", "response_code": 503, "bytes": 9468}
{ "index" : { "_index" : "<index>" } }
{"timestamp": 1557933742, "message": "DELETE /reinvent/interfaces", "host": "Herman3087", "process_id": 775, "remote_addr": "43.246.221.247", "response_code": 503, "bytes": 9700}
```

## How It Works

### Buffers & Batches

![][assets.sink-flow-serial]

The `elasticsearch` sink buffers & batches data as
shown in the diagram above. You'll notice that Vector treats these concepts
differently, instead of treating them as global concepts, Vector treats them
as sink specific concepts. This isolates sinks, ensuring services disruptions
are contained and [delivery guarantees][docs.guarantees] are honored.

#### Buffers types

The `buffer.type` option allows you to control buffer resource usage:

| Type     | Description                                                                                                    |
|:---------|:---------------------------------------------------------------------------------------------------------------|
| `memory` | Pros: Fast. Cons: Not persisted across restarts. Possible data loss in the event of a crash. Uses more memory. |
| `disk`   | Pros: Persisted across restarts, durable. Uses much less memory. Cons: Slower, see below.                      |

#### Buffer overflow

The `buffer.when_full` option allows you to control the behavior when the
buffer overflows:

| Type          | Description                                                                                                                        |
|:--------------|:-----------------------------------------------------------------------------------------------------------------------------------|
| `block`       | Applies back pressure until the buffer makes room. This will help to prevent data loss but will cause data to pile up on the edge. |
| `drop_newest` | Drops new data as it's received. This data is lost. This should be used when performance is the highest priority.                  |

#### Batch flushing

Batches are flushed when 1 of 2 conditions are met:

1. The batch age meets or exceeds the configured `batch_timeout` (default: `1 seconds`).
2. The batch size meets or exceeds the configured `batch_size` (default: `10490000 bytes`).

### Delivery Guarantee

Due to the nature of this component, it offers a
[**best effort** delivery guarantee][docs.guarantees#best-effort-delivery].

### Environment Variables

Environment variables are supported through all of Vector's configuration.
Simply add `${MY_ENV_VAR}` in your Vector configuration file and the variable
will be replaced before being evaluated.

You can learn more in the [Environment Variables][docs.configuration#environment-variables]
section.

### Health Checks

Health checks ensure that the downstream service is accessible and ready to
accept data. This check is performed upon sink initialization.

If the health check fails an error will be logged and Vector will proceed to
start. If you'd like to exit immediately upon health check failure, you can
pass the `--require-healthy` flag:

```bash
vector --config /etc/vector/vector.toml --require-healthy
```

And finally, if you'd like to disable health checks entirely for this sink
you can set the `healthcheck` option to `false`.

### Nested Documents

Vector will explode events into nested documents before writing them to
Elasticsearch. Vector assumes keys with a . delimit nested fields. You can read
more about how Vector handles nested documents in the [Data Model
document][docs.data_model].

### Rate Limits

Vector offers a few levers to control the rate and volume of requests to the
downstream service. Start with the `rate_limit_duration` and `rate_limit_num`
options to ensure Vector does not exceed the specified number of requests in
the specified window. You can further control the pace at which this window is
saturated with the `request_in_flight_limit` option, which will guarantee no
more than the specified number of requests are in-flight at any given time.

Please note, Vector's defaults are carefully chosen and it should be rare that
you need to adjust these. If you found a good reason to do so please share it
with the Vector team by [opening an issie][urls.new_elasticsearch_sink_issue].

### Retry Policy

Vector will retry failed requests (status == `429`, >= `500`, and != `501`).
Other responses will _not_ be retried. You can control the number of retry
attempts and backoff rate with the `retry_attempts` and `retry_backoff_secs` options.

### Template Syntax

The `index` options
support [Vector's template syntax][docs.configuration#template-syntax],
enabling dynamic values derived from the event's data. This syntax accepts
[strftime specifiers][urls.strftime_specifiers] as well as the
`{{ field_name }}` syntax for accessing event fields. For example:

{% code-tabs %}
{% code-tabs-item title="vector.toml" %}
```coffeescript
[sinks.my_elasticsearch_sink_id]
  # ...
  index = "vector-%Y-%m-%d"
  index = "application-{{ application_id }}-%Y-%m-%d"
  # ...
```
{% endcode-tabs-item %}
{% endcode-tabs %}

You can read more about the complete syntax in the
[template syntax section][docs.configuration#template-syntax].

### Timeouts

To ensure the pipeline does not halt when a service fails to respond Vector
will abort requests after `60 seconds`.
This can be adjsuted with the `request_timeout_secs` option.

It is highly recommended that you do not lower value below the service's
internal timeout, as this could create orphaned requests, pile on retries,
and result in deuplicate data downstream.

## Troubleshooting

The best place to start with troubleshooting is to check the
[Vector logs][docs.monitoring#logs]. This is typically located at
`/var/log/vector.log`, then proceed to follow the
[Troubleshooting Guide][docs.troubleshooting].

If the [Troubleshooting Guide][docs.troubleshooting] does not resolve your
issue, please:

1. Check for any [open `elasticsearch_sink` issues][urls.elasticsearch_sink_issues].
2. If encountered a bug, please [file a bug report][urls.new_elasticsearch_sink_bug].
3. If encountered a missing feature, please [file a feature request][urls.new_elasticsearch_sink_enhancement].
4. If you need help, [join our chat/forum community][urls.vector_chat]. You can post a question and search previous questions.

## Resources

* [**Issues**][urls.elasticsearch_sink_issues] - [enhancements][urls.elasticsearch_sink_enhancements] - [bugs][urls.elasticsearch_sink_bugs]
* [**Source code**][urls.elasticsearch_sink_source]


[assets.elasticsearch_sink]: ../../../assets/elasticsearch-sink.svg
[assets.sink-flow-serial]: ../../../assets/sink-flow-serial.svg
[docs.configuration#environment-variables]: ../../../usage/configuration#environment-variables
[docs.configuration#template-syntax]: ../../../usage/configuration#template-syntax
[docs.data-model.log]: ../../../about/data-model/log.md
[docs.data_model]: ../../../about/data-model
[docs.guarantees#best-effort-delivery]: ../../../about/guarantees.md#best-effort-delivery
[docs.guarantees]: ../../../about/guarantees.md
[docs.monitoring#logs]: ../../../usage/administration/monitoring.md#logs
[docs.troubleshooting]: ../../../usage/guides/troubleshooting.md
[urls.elasticsearch]: https://www.elastic.co/products/elasticsearch
[urls.elasticsearch_sink_bugs]: https://github.com/timberio/vector/issues?q=is%3Aopen+is%3Aissue+label%3A%22sink%3A+elasticsearch%22+label%3A%22Type%3A+bug%22
[urls.elasticsearch_sink_enhancements]: https://github.com/timberio/vector/issues?q=is%3Aopen+is%3Aissue+label%3A%22sink%3A+elasticsearch%22+label%3A%22Type%3A+enhancement%22
[urls.elasticsearch_sink_issues]: https://github.com/timberio/vector/issues?q=is%3Aopen+is%3Aissue+label%3A%22sink%3A+elasticsearch%22
[urls.elasticsearch_sink_source]: https://github.com/timberio/vector/tree/master/src/sinks/elasticsearch.rs
[urls.new_elasticsearch_sink_bug]: https://github.com/timberio/vector/issues/new?labels=sink%3A+elasticsearch&labels=Type%3A+bug
[urls.new_elasticsearch_sink_enhancement]: https://github.com/timberio/vector/issues/new?labels=sink%3A+elasticsearch&labels=Type%3A+enhancement
[urls.new_elasticsearch_sink_issue]: https://github.com/timberio/vector/issues/new?labels=sink%3A+elasticsearch
[urls.strftime_specifiers]: https://docs.rs/chrono/0.3.1/chrono/format/strftime/index.html
[urls.vector_chat]: https://chat.vector.dev
