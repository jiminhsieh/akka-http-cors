# akka-http-cors

## WIP
This project is under development.

## Introduction
CORS (Cross Origin Resource Sharing) is a mechanism to enable cross origin requests.

This is a Scala implementation for the server-side targeting the akka-http 2.x library. Main features:
- [x] Works without any additional configuration. Sensible defaults are provided.
- [x] Respects the full standard defined by the W3C, even the border cases.
- [ ] Tests, lots of tests.

## Quick Start
This project is not deployed to maven. If you want to try it, just copy the unique source file inside your project.

The simplest way to enable CORS in your application is to use the `cors` directive.
Settings are passed as a parameter to the directive, with defaults provided for convenience.
```scala
val route: Route = cors() {
  complete(...)
}
```

The default settings can be used as a baseline to customize the CORS directive behaviour:
```scala
val settings = CorsSettings.defaultSettings.copy(allowGenericHttpRequests = false)
val strictRoute: Route = cors(settings) {
  complete(...)
}
```

A second directive, `corsDecorate`, implements the same behaviour as the first one but additionally provides information about the current request to the inner route.
```scala
val route: Route = corsDecorate() {
  case CorsRequest(origins) ⇒ complete("actual")
  case NotCorsRequest       ⇒ complete("not cors")
}
```

## Configuration

#### allowGenericHttpRequests
`Boolean` with default value `true`.

If `true`, allow generic requests (that are outisde the scope of the specification) to pass through the directive. Else, strict CORS filtering is applied and any invalid request will be rejected.

#### allowCredentials
`Boolean` with default value `true`.

Controls the presence of the `Access-Control-Allow-Credentials` header in the response. From the [W3C page](https://www.w3.org/TR/cors/#access-control-allow-credentials-response-header):

> The `Access-Control-Allow-Credentials` header indicates whether the response to request can be exposed when the omit credentials flag is unset. When part of the response to a preflight request it indicates that the actual request can include user credentials. — _W3C_

Examples of user credentials are: cookies, HTTP authentication or client-side certificates.

#### allowedOrigins
`HttpOriginRange` with default value `HttpOriginRange.*`.

List of origins that the CORS filter must allow. Can also be set to `*` to allow any origin to access the resource. Controls the content of the `Access-Control-Allow-Origin` response header using the following logic:
* if parameter is `*` **and** credentials are not allowed, the `*` is returned.
* otherwise, the origins given in the `Origin` request header are echoed.

#### allowedHeaders
`HttpHeaderRange` with default value `HttpHeaderRange.*`.

#### allowedMethods
`Seq[HttpMethod]` with default value `Seq(GET, POST, HEAD, OPTIONS)`.

List of methods allowed by the CORS filter. Controls the content of the `Access-Control-Allow-Methods`
response header. From the [W3C page](https://www.w3.org/TR/cors/#access-control-max-age-response-header):

> The `Access-Control-Allow-Methods` header indicates, as part of the response to a preflight request, which methods can be used during the actual request.  — _W3C_

The preflight request will be rejected if the `Access-Control-Request-Method` header's method is not part of the list.

#### exposedHeaders
`Seq[String]` with default value `Seq.empty`.

#### maxAge
`Option[Long]` (in seconds) with default value `Some (30 * 60)`.

Controls the `Access-Control-Max-Age` response header. From the [W3C page](https://www.w3.org/TR/cors/#access-control-max-age-response-header):

> The `Access-Control-Max-Age` header indicates how long the results of a preflight request can be cached in a preflight result cache. — _W3C_

## References
- [W3C Specification: CORS](https://www.w3.org/TR/cors/)
- [RFC-6454: The Web Origin Concept](https://tools.ietf.org/html/rfc6454)
