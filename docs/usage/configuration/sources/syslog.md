---
description: Ingests data through the Syslog 5424 protocol and outputs `log` events.
---

<!--
     THIS FILE IS AUTOGENERATED!

     To make changes please edit the template located at:

     scripts/generate/templates/docs/usage/configuration/sources/syslog.md.erb
-->

# syslog source

![][assets.syslog_source]


The `syslog` source ingests data through the Syslog 5424 protocol and outputs [`log`][docs.data-model.log] events.

## Config File

{% code-tabs %}
{% code-tabs-item title="vector.toml (simple)" %}
```coffeescript
[sources.my_source_id]
  type = "syslog" # must be: "syslog"
  mode = "tcp" # enum: "tcp", "udp", and "unix"

  # For a complete list of options see the "advanced" tab above.
```
{% endcode-tabs-item %}
{% code-tabs-item title="vector.toml (advanced)" %}
```coffeescript
[sources.syslog_source]
  #
  # General
  #

  # The component type
  # 
  # * required
  # * no default
  # * must be: "syslog"
  type = "syslog"

  # The input mode.
  # 
  # * required
  # * no default
  # * enum: "tcp", "udp", and "unix"
  mode = "tcp"
  mode = "udp"
  mode = "unix"

  # The TCP or UDP address to listen on.
  # 
  # * optional
  # * no default
  address = "0.0.0.0:9000"

  # The maximum bytes size of incoming messages before they are discarded.
  # 
  # * optional
  # * default: 102400
  # * unit: bytes
  max_length = 102400

  # The unix socket path. *This should be absolute path.*
  # 
  # * optional
  # * no default
  path = "/path/to/socket"

  #
  # Context
  #

  # The key name added to each event representing the current host.
  # 
  # * optional
  # * default: "host"
  host_key = "host"
```
{% endcode-tabs-item %}
{% endcode-tabs %}

## Examples

Given the following input line:

{% code-tabs %}
{% code-tabs-item title="stdin" %}
Given the following input

```
<34>1 2018-10-11T22:14:15.003Z mymachine.example.com su - ID47 - 'su root' failed for lonvick on /dev/pts/8
```
{% endcode-tabs-item %}
{% endcode-tabs %}

A [`log` event][docs.data-model.log] will be emitted with the following structure:

{% code-tabs %}
{% code-tabs-item title="log" %}
```javascript
{
  "timestamp": <2018-10-11T22:14:15.003Z> # current time,
  "message": "<34>1 2018-10-11T22:14:15.003Z mymachine.example.com su - ID47 - 'su root' failed for lonvick on /dev/pts/8",
  "host": "mymachine.example.com",
  "peer_path": "/path/to/unix/socket" # only relevant if `mode` is `unix`
}
```

Vector only extracts the `"timestamp"` and `"host"` fields and leaves the
`"message"` in-tact. You can further parse the `"message"` key with a
[transform][docs.transforms], such as the
[`regex_parser` transform][docs.transforms.regex_parser].
{% endcode-tabs-item %}
{% endcode-tabs %}

## How It Works

### Context

By default, the `syslog` source will add context
keys to your events via the `host_key`
options.

### Delivery Guarantee

Due to the nature of this component, it offers a
[**best effort** delivery guarantee][docs.guarantees#best-effort-delivery].

### Environment Variables

Environment variables are supported through all of Vector's configuration.
Simply add `${MY_ENV_VAR}` in your Vector configuration file and the variable
will be replaced before being evaluated.

You can learn more in the [Environment Variables][docs.configuration#environment-variables]
section.

### Line Delimiters

Each line is read until a new line delimiter (the `0xA` byte) is found.

### Parsing

Vector will parse messages in the [Syslog 5424][urls.syslog_5424] format.

#### Successful parsing

Upon successful parsing, Vector will create a structured event. For example, given this Syslog message:

```
<13>1 2019-02-13T19:48:34+00:00 74794bfb6795 root 8449 - [meta sequenceId="1"] i am foobar
```

Vector will produce an event with this structure.

```javascript
{
  "message": "<13>1 2019-02-13T19:48:34+00:00 74794bfb6795 root 8449 - [meta sequenceId="1"] i am foobar",
  "timestamp": "2019-02-13T19:48:34+00:00",
  "host": "74794bfb6795"
}
```

#### Unsuccessful parsing

Anyone with Syslog experience knows there are often deviations from the Syslog specifications. Vector tries its best to account for these (note the tests here). In the event Vector fails to parse your format, we recommend that you open an issue informing us of this, and then proceed to use the `tcp`, `udp`, or `unix` source coupled with a parser [transform][docs.transforms] transform of your choice.

## Troubleshooting

The best place to start with troubleshooting is to check the
[Vector logs][docs.monitoring#logs]. This is typically located at
`/var/log/vector.log`, then proceed to follow the
[Troubleshooting Guide][docs.troubleshooting].

If the [Troubleshooting Guide][docs.troubleshooting] does not resolve your
issue, please:

1. Check for any [open `syslog_source` issues][urls.syslog_source_issues].
2. If encountered a bug, please [file a bug report][urls.new_syslog_source_bug].
3. If encountered a missing feature, please [file a feature request][urls.new_syslog_source_enhancement].
4. If you need help, [join our chat/forum community][urls.vector_chat]. You can post a question and search previous questions.

## Resources

* [**Issues**][urls.syslog_source_issues] - [enhancements][urls.syslog_source_enhancements] - [bugs][urls.syslog_source_bugs]
* [**Source code**][urls.syslog_source_source]


[assets.syslog_source]: ../../../assets/syslog-source.svg
[docs.configuration#environment-variables]: ../../../usage/configuration#environment-variables
[docs.data-model.log]: ../../../about/data-model/log.md
[docs.guarantees#best-effort-delivery]: ../../../about/guarantees.md#best-effort-delivery
[docs.monitoring#logs]: ../../../usage/administration/monitoring.md#logs
[docs.transforms.regex_parser]: ../../../usage/configuration/transforms/regex_parser.md
[docs.transforms]: ../../../usage/configuration/transforms
[docs.troubleshooting]: ../../../usage/guides/troubleshooting.md
[urls.new_syslog_source_bug]: https://github.com/timberio/vector/issues/new?labels=source%3A+syslog&labels=Type%3A+bug
[urls.new_syslog_source_enhancement]: https://github.com/timberio/vector/issues/new?labels=source%3A+syslog&labels=Type%3A+enhancement
[urls.syslog_5424]: https://tools.ietf.org/html/rfc5424
[urls.syslog_source_bugs]: https://github.com/timberio/vector/issues?q=is%3Aopen+is%3Aissue+label%3A%22source%3A+syslog%22+label%3A%22Type%3A+bug%22
[urls.syslog_source_enhancements]: https://github.com/timberio/vector/issues?q=is%3Aopen+is%3Aissue+label%3A%22source%3A+syslog%22+label%3A%22Type%3A+enhancement%22
[urls.syslog_source_issues]: https://github.com/timberio/vector/issues?q=is%3Aopen+is%3Aissue+label%3A%22source%3A+syslog%22
[urls.syslog_source_source]: https://github.com/timberio/vector/tree/master/src/sources/syslog.rs
[urls.vector_chat]: https://chat.vector.dev
