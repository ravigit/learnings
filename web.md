## Clojure http server notes

### Terminology:

Ring : A protocol for creating webapps. It dictates the architecture of request/response stack, such that other common useful things can built that can be used with webapps

Handler : Function that handles a request.

Middleware: A intermediary layer in request/response cycle, that decorates the request, response or asserts something about the request/response. Middleware can be reused across the webapps

Router: A function/app that directs requests to appropriate handlers.

Parameter coercion: Type checking?

Interceptor: How is this different from middleware?
