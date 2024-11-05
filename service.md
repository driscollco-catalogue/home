# Service Library

The service library is a Go library which makes it easy to create a microservice
with the minimum of scaffolding. It contains an http router, an instance of
the log library, a process library which allows easy management of multiple 
goroutines and a config management library.

Some of these libraries propagate down to individual http requests or 
goroutine processes as the `service` library supplies them via calls on the
libraries it bundles, eg. on setup, the `service` library can call `WithLog()`
or `WithDb` to pass on specific logs, database connections or other resources.
This happens 'behind the scenes' so as long as you supply these resources to the
service library, you will find them available in your goroutines and your 
http request processing via functions like `Log()` or `Db()`. You can see an 
example of this in the code below.

## Included Libraries

* [Config Library](https://github.com/driscollos/config)
* [Http Router](router.md)
* [Process Group](https://github.com/driscollco-core/process-group)
* [Log](https://github.com/driscollco-core/log)

## Example Microservice

Here is an example of a very simple microservice which uses the library. In a 
real world example things like the endpoints would be in separate modules etc.

```go
func main() {
    s := service.New("example service")
    s.Router().Get("/", handlerBasic.Handle)
    s.Router().Get("/:one", handlerBasic.Handle)
	
    // The shutdown function runs when the service is shut down, after the
    // router has stopped serving requests
    s.Router().WithShutdownFunc(func(resources router.Resources) {
        resources.Log().Info("http router shutdown function processing")
    })
	
    // This defines a new parallel goroutine which will run alongside the main goroutine
    if err := s.ProcessGroup().Process(pinger, "pinger"); err != nil {
        s.Log().Error("unable to launch pinger process", "error", err.Error())
        os.Exit(0)
    }

    // This shutdown funtion runs when the process group is shut down and it has
    // stopped all it's goroutines from executing 
    s.ProcessGroup().WithShutdownFunc(func(resources processGroupInterfaces.Resources) {
        resources.Log().Info("processgroup shutdown function processing")
    })
	
    // The service library has a bundled copy of the github.com/driscollos/config library
    if err := s.Config().Populate(&conf.Config); err != nil {
        s.Log().Error("unable to populate config", "error", err.Error())
        os.Exit(0)
    }

    // The service library catches and handles all unix quit signals
    s.Run()
}

// This is an example of a function which can be a processgroup goroutine
// It is used above to create a goroutine
func pinger(process processGroupInterfaces.Process) error {
    process.Log().Info("ping")
    return nil
}
```