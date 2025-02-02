# Libraries

## Config

[![Repository](https://img.shields.io/badge/Repository-github.com/driscollos/config%20-blue)](https://github.com/driscollos/config)

The [Config library](https://github.com/driscollos/config) is a library which manages configuration of services. Configuration
information is sourced from three places; the commandline, environment variables and yaml or json files. You can access
configuration information either by direct access functions eg `.String("variableName")` or `.Int("variableName")` or by
populating a struct.

## Http Router

[![Repository](https://img.shields.io/badge/Repository-github.com/driscollco--core/http--router%20-blue)](https://github.com/driscollco-core/http-router)

The [http router library](router.md) is an http router which attempts to make writing a
REST service as trivial as possible. It uses a very simplified format for
handlers and bundles in facilities such as a log library and a fireStore database
connection.

## Service

[![Repository](https://img.shields.io/badge/Repository-github.com/driscollco--core/service%20-blue)](https://github.com/driscollco-core/service)

The [service library](service.md) is a Go library which makes it easy to create a microservice
with the minimum of scaffolding. It contains an http router, a process library
which allows easy management of multiple goroutines and a config management
library. 

# Services

## Shortify.pro

[<img src="/img/shortify.png" width="350px" align="left" style="margin-right: 50px;"/>](https://shortify.pro)

[Shortify.pro](https://shortify.pro) is a URL shortening site. It is used as a testbed for advanced
software techniques. It has an event driven architecture handled by [GCP Pub/Sub](https://cloud.google.com/pubsub/docs/overview)
and uses [GCP FireStore](https://cloud.google.com/firestore#all-features) for data storage.

<br clear="left"/>

## Flow Control

[<img src="/img/flow.png" width="350px" align="left" style="margin-right: 50px;"/>](https://flow.driscoll.co)

[Flow Control](https://flow.driscoll.co) is a project to manage rate limiting without the need for api gateways.
It is entirely cloud based; it comprises a number of GCP Cloud Run services and GCP fireStore for state management.

<br clear="left"/>