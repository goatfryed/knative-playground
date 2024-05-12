# Rust HTTP Function

## Deployment
Use `func` to containerize your application, publish it to a registry
and deploy it as a Knative Service in your Kubernetes cluster

Atm we focus on local setup with k3d

The deploy will probably yield an error as dev.local is unknown. But it still creates the func in docker deamon, which is what we want
```shell
func deploy --registry=local.dev/knative-playground/rust-sample-function
```

Next, we can create/update the function, since the previous command failed to complete.
We never pull the image, to always use the local one.
For this, we need to push the image from local docker daemon into k3d first

```shell
k3d image import -c knative-playground \
  local.dev/knative-playground/rust-sample-function

kn service update rust-sample-function \
  --image local.dev/knative-playground/rust-sample-function \
  --port 8080 \
  --pull-policy Never \
  --no-lock-to-digest
```
You can omit the `--registry` option by setting the `FUNC_REGISTRY`
environment variable. And if you forget, you'll be prompted.

The output from a successful deploy should show the URL for the
service, which you can also get via `func info`, e.g.

```console
curl $(func info -o url)
```

Have fun!

--------
Default README beyond this point. todo: cleanup

Welcome to your new Rust function project! The boilerplate
[actix](https://actix.rs/) web server is in
[`src/main.rs`](./src/main.rs). It's configured to invoke the `index`
function in [`src/handler.rs`](./src/handler.rs) in response to both
GET and POST requests. You should put your desired behavior inside
that `index` function. In case you need to configure
some resources for your function, you can do that in the [`configure` function](./src/config.rs).

The app will expose three endpoints:

  * `/` Triggers the `index` function, for either GET or POST methods
  * `/health/readiness` The endpoint for a readiness health check
  * `/health/liveness` The endpoint for a liveness health check

You may use any of the available [actix
features](https://actix.rs/docs/) to fulfill the requests at those
endpoints.

## Development

This is a fully self-contained application, so you can develop it as
you would any other Rust application, e.g.

```shell script
cargo build
cargo test
cargo run
```

Once running, the function is available at <http://localhost:8080> and
the health checks are at <http://localhost:8080/health/readiness> and
<http://localhost:8080/health/liveness>. To POST data to the function,
a utility such as `curl` may be used:

```console
curl -d '{"hello": "world"}' \
  -H'content-type: application/json' \
  http://localhost:8080
```

