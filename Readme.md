# Libraries

## Config

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