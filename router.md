# Http Router

[![Repository](https://img.shields.io/badge/Repository-github.com/driscollco--core/http--router%20-blue)](https://github.com/driscollco-core/http-router)

The `http router` library is an http router which attempts to make writing a 
REST service as trivial as possible. It uses a very simplified format for 
handlers and bundles in facilities such as a log library and a fireStore database
connection.

## Defining A Route

The following four lines set up a new http server which listens on port `80` (default) and will send all requests for the endpoint
`/` to the handler defined at `handlerBasic.Handle`. The third line defines that any requests for a url starting with `/arg/` will
also go to the basic handler. The `:one` part of the path denotes an optional variable, `one`. You can make a variable required by
wrapping it in square brackets eg. `/arg/[:one]`.

```go
r := router.Router{}
r.Get("/", handlerBasic.Handle)
r.Get("/arg/:one", handlerBasic.Handle)
r.Serve()
```

## Example Http Handler

This handler works with the example above. You can see how easy it is to check if the argument `one` exists in the request
URL and if so, retrieve the value of it. You can also see how the http router makes it easy to grab a logger.

```go
func Handle(request router.Request) router.Response {
    // This creates a new child log which prepends the key/value data
    // handler -> 'myHttpHandler' to all log entries
    log := request.Log().Child("handler", "myHttpHandler")
    log.Info("started processing")

    // It is trivial to check if arguments are defined in the URL 
    if request.ArgExists("one") {
        log.Info("got an argument in the URL", "one", request.GetArg("one"))
        return request.Success(fmt.Sprintf("received: %s", request.GetArg("one")))
    }
    log.Info("no argument received in URL")

    // Success() by default returns an HTTP 200 status code and any content
    // you like. You can override the http status code by making the first
    // argument any valid http status code eg.
    // return request.Success(http.StatusCreated, "one", "two", 3)
    // You can also use a struct as an argument to Success() and it will
    // automatically be translated to json
    return request.Success("OK")
}
```

## Methods

* `Delete(path string, handler Handler)` - define a route where requests will be `DELETE` requests
* `Get(path string, handler Handler)` - define a route where requests will be `GET` requests
* `NotFound(handler Handler)` - supply a handler for any request which does not match any defined route
* `Patch(path string, handler Handler)` - define a route where requests will be `PATCH` requests
* `Post(path string, handler Handler)` - define a route where requests will be `POST` requests
* `Put(path string, handler Handler)` - define a route where requests will be `PUT` requests
* `Serve() error` - start serving traffic
* `WithCache(cache *cache.Cache)` - supply a cache library which will be made available to all handlers
* `WithContext(ctx context.Context)` - supply a context which your wider service may be using to track os signals. Http router will
stop accepting connections when the supplied context is `done`
* `WithDb(db fireStore.Client)` - supply a fireStore client which will be made available to all handlers
* `WithIp(ip string)` - listen on the specified ip address
* `WithLog(log log.Log)` - supply a logger which will be made available to all handlers
* `WithShutdownFunc(closeFunc OnShutdown)` - supply a function which will execute after the router has stopped accepting connections
  (`serve()` will wait until this function has finished executing before completing)
* `WithPort(port int)` - listen on the specified port
* `WithTLS(key, cert string)` - use TLS with the supplied key and certificate (leave blank and the router will generate a self
signed cert)

## Request Methods

When writing a handler, the only parameter your handler should accept is an instance of `router.Request`. This replaces the
traditional structure of `(w ResponseWriter, r *Request)`. There is nothing wrong with these (in fact the `http router` uses these 
under the hood) but the http router aims to simplify writing a router as much as is possible. The following methods 
of the `router.Request` help you to work with an incoming http request and craft a response:

* `ArgExists(name string) bool` - Check if a URL argument exists or not
* `Body() []byte` - Get the body of a request (for methods like `POST`)
* `BodyError() error` - If there was an issue retrieving the body of a request, examine that via this method
* `Cache() cache.Cache` - Get an instance of a cache, shared across all requests (thread safe)
* `Context() context.Context` - Get the `context` of the incoming request
* `Db() fireStore.Client` - Get a ready to go client for `GCP FireStore` - this is supplied to the `http router` on service setup
* `GetArg(name string) string` - Get the value of the specified url argument
* `GetHeader(header string) string` - Get the specified HTTP header (blank if the header is not present)
* `GetHeaders() map[string][]string` - Get a map of all HTTP headers sent as part of the incoming request
* `GetHost() string` - Get the hostname the incoming request was sent to
* `GetIp() string` - Get the `ip address` of the client making the http request
* `GetPostVariable(name string) string` - Get a traditional `POST` variable
* `GetReferer() string` - Get the `REFERER` header if one was present
* `GetURL() string` - Get the complete URL of the incoming request
* `GetUserAgent() string` - Get the `User-Agent` header if present
* `HasBody() bool` - Simple boolean check; did this request have a body of `> 0` length
* `HeaderExists(header string) bool` - Boolean check; did this request include the specified header
* `Log() log.Log` - Get an instance of a logger
* `PostVariableExists(name string) bool` - Simple boolean check; does the specified `POST` variable exist as part of this request
* `Error(response ...interface{}) Response` - Respond to the client indicating a failed request. If the first parameter is an 
`HTTP status code` eg. `http.StatusBadRequest`, this is the status code that goes back to the client. Add as many parameters as
you like. If any parameter is a `struct` this will automatically be converted to `json`. The default status code if none
is specified is `http.StatusBadRequest`
* `PermanentRedirect(destination string) Response` - Respond with a permanent redirect to the supplied destination
* `Redirect(destination string) Response` - Respond with a temporary redirect to the supplied destination
* `Response(response ...interface{}) Response` - Respond to the client. If the first parameter is an
  `HTTP status code` eg. `http.StatusOK`, this is the status code that goes back to the client. Add as many parameters as
  you like. If any parameter is a `struct` this will automatically be converted to `json`. The default status code if none
  is specified is `http.StatusOK`
* `SetResponseHeader(key, val string)` - Set a header to be included with your response
* `Success(response ...interface{}) Response` - Respond to the client indicating a successful request. If the first parameter is an
  `HTTP status code` eg. `http.StatusOK`, this is the status code that goes back to the client. Add as many parameters as
  you like. If any parameter is a `struct` this will automatically be converted to `json`. The default status code if none
  is specified is `http.StatusOK`