# Kubernetes built with Earthly

<img src="https://github.com/kubernetes/kubernetes/raw/master/logo/logo.png" width="200"><a href="https://earthly.dev"><img src="logo/logo-banner-white-bg.png" hspace="50px" alt="Earthly" width="300" /></a>


----

[Earthly](https://earthly.dev) is a very pragmatic iteration from the existing ecosystem of container build
tools available today. It combines the best of Dockerfiles and Makefiles to provide a target-based containerized
build system to provide consistently repeatable and reproducible builds. Earthly takes it a step further by understanding
target dependencies and running targets in parallel when possible by leveraging the isolation characteristics of containers.

This project uses Earthly to build the Kubernetes project. See the [Earthfile](./Earthfile) to see how it can be adopted
for any existing project that may already be using Docker or Make, even for large projects like Kubernetes.

----

## Example targets

Here are some examples of common targets to run.

Run all unit and integration tests:
```
$ earthly +test-all
```

Run all verify checks:
```
$ earthly -P +verify
```

Run all update scripts:
```
$ earthly +update
```

Build components:
```
$ earthly +build
```

Build a specific component:
```
$ earthly --build-arg WHAT=kubelet +build
```

Produce Kubernetes release artifacts:
```
$ earthly -P +release
```

## Build Time Improvements


## Caveats

Some improvements to Earthly were required in order to use it to build the Kubernetes project:
- Earthfile feature flag `--no-implicit-ignore` (https://github.com/earthly/earthly/pull/1297)

Ensure you are using a version of Earthly with these changes.
