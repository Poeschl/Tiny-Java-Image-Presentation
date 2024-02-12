# Example Code for tiny Java images

The artifact in this folder contains a simple Spring Boot Application which serves a funny cat picture at http://localhost:8080.

It will be included in a container image via the Dockerfile.

## Build

```bash
podman build -t tiny-jdk .
```

## Run

```bash
podman run --rm tiny-jdk
```
