# rules_nodejs cookbook 

Recipes, unexpected pitfalls and other non-trivial gotchas for [rules_nodejs](https://github.com/bazelbuild/rules_nodejs/)

# Test suites support in context of Bazel

## Unit tests (TDD, BDD)

+ karma — [fully supported](https://www.npmjs.com/package/@bazel/karma), headless and watch mode [are available](https://bazelbuild.github.io/rules_nodejs/Karma.html).
    
+ jasmine — [fully supported](https://bazelbuild.github.io/rules_nodejs/Jasmine.html).

- jest — not supported in `rules_nodejs`. Possible solutions:

  1. https://github.com/ecosia/bazel_rules_nodejs_contrib/tree/master/examples/jest_node_test


## e2e tests

+ protractor — [fully supported](https://bazelbuild.github.io/rules_nodejs/Protractor.html).

- cypress — [not supported](https://github.com/bazelbuild/rules_nodejs/issues/607), could be used with workarounds possibly.


# Build and compatibility issues

### Node 11 and 12 with modules relying on node-pre-gyp does not work

Sometimes it works, sometimes fails: [non-consistent issue described here](https://github.com/bazelbuild/rules_nodejs/issues/930) (easy to reproduce though).

Fails only with Bazel build somehow, yarn install under `node:11` or `node:12` docker container works fine.

Possible workarounds:

- Switch to Node 10 (e.g. do not configure `node_repositories` or configure it to use node 10). 10 works fine with Bazel.

# Docker

### Cross-platform builds (targeting linux on Mac OS or Windows)

It is possible to build Node binaries targeting another platform since `rules_nodejs` 0.34.0 — [related PR](https://github.com/bazelbuild/rules_nodejs/pull/827).

But still not possible to cross-compile node_modules (when they require native bindings, like bcrypt, etc.).

Possible workarounds:

- When project contain modules relying on native bindings, build it’s docker images under Bazel docker:

```bash
docker run -it --rm -v "$PWD":/app -w /app --entrypoint=/bin/bash l.gcr.io/google/bazel:0.28.0
apt update && apt install -y build-essential
bazel build //src:docker
```


### Issue with layers order

`nodejs_image` is [messing up docker layers](https://github.com/bazelbuild/rules_nodejs/pull/863) for node_modules.

Partially resolved [here](https://github.com/bazelbuild/rules_docker/pull/1000) in context of `rules_docker` (not in context of `rules_nodejs` yet).


### Debian 9 vs distroless

`nodejs_image` in [rules_docker](https://github.com/bazelbuild/rules_docker) is based on [debian9 base image](https://github.com/bazelbuild/rules_docker/blob/b361f3b7982ad3633daa0a4ad2cb44890c74177b/nodejs/image.bzl#L51):

Cloud Security Scanner for GKE [is reporting](https://github.com/bazelbuild/rules_docker/issues/1068) some security issues for it.

All of them are tolerable for production environment at the moment.

Reasons to [use debian-based instead of distroless](https://github.com/bazelbuild/rules_docker/pull/254):

- nodejs_binary runfiles includes the node binary, so there is no need of nodejs runtime in the base layer.

- generated `nodejs_binary` entrypoint is currently a bash script wrapping node with some additional logic, so base runtime is needed to run this logic 

Possible workarounds:

Switch base image to something else?


### Issue with PORT env

If app is using PORT env variable, make sure to define it within container_image or default `8080` [will be used instead](https://github.com/GoogleContainerTools/base-images-docker/blob/6e6fdf4ac44ca1cf8f3dfd6fd91f9fc0dae5ffbb/debian9/BUILD#L71).

Possible workarounds:

```python
load("@io_bazel_rules_docker//container:container.bzl", "container_image")
container_image(
    name = "modified_base_image",
    base = "@nodejs_image_base//image",
    env = {
        "PORT": "3000",
    },
)
nodejs_image(
    name = "docker",
    base = ":modified_base_image",
    entry_point = ":main.ts",
    node_modules = "@npm//:node_modules",
    data = [":app"],
)
```

