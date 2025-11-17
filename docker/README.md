# Docker and Scripts

We provide some scripts and Dockerfiles to simplify developing and testing the zkGuard program.

## TLDR

Compile the program:

```bash
> . cfg
> ./docker/scripts/bootstrap.sh
```

Start a local Solana test validator with the program deployed:

```bash
> docker compose -f docker/compose/zkguard-docker-compose.yaml up
```

## Scripts

```text
├── cfg                   # Add scripts path to your PATH environment variable
├── docker
│   └── scripts
│       ├── bootstrap.sh  # Compile and generate an image with injected program binary
│       ├── build-zkguard-image-injected.sh # Create an image with injected program binary
│       ├── build-injected.sh # Create an image with injected binary
│       └── my_cargo      # Use cargo from a docker image with all dependencies installed but
                          # leverage host environment for caching: useful to avoid
                          # problems related to incompatible glibc versions without lost 
                          # cache compilation
...
```

### `bootstrap.sh`

The simple workflow is:

```bash
> . cfg # Just the first time
> ./docker/scripts/bootstrap.sh
```

This will compile the program and create a Docker image with the program binary.

Run the program in a Docker container:

```bash
> docker run -ti --rm zkguard --dev
```

The `zkguard` binary is also available on your host environment at `target/deploy/` location.

### `my_cargo`

`my_cargo` is a simple `cargo` replacement that execute the command inside a `rbuilder` docker container. `rbuilder` has all Rust's dependencies installed and use the host's environment to inherit cargo's repository cache, user github credentials to fetch the private repository and the local target folder to save binaries and incremental compilation artifacts.

### `build-injected.sh`

A script that generate a docker image with the base dependencies and the given executables injected. See `build-zkguard-image-injected.sh` as example of how to use it.

## Docker and Compose

All Dockerfiles are located in `docker/dockerfiles` folder.

* `zkguard-builder.Dockerfile`: create an image with all dependencies needed to compile the program and is used by `my_cargo` script
* `binary_injected.Dockerfile`: mainly used by the scripts and inject one or more binaries in a standard ubuntu image
* `zkguard.Dockerfile`: generate a program image with a fresh source compilation (leverage on docker layers to create a small docker image)

All compose definitions are located in `docker/compose` folder.

* `zkguard-docker-compose.yaml`: the cluster definition that runs a local Solana test validator with the program deployed

To generate a program image without bothering about local resources, Rust installation and so on you can simply use:

```bash
> docker build -f docker/dockerfiles/zkguard.Dockerfile -t zkguard:latest .
```

and run it with

```bash
> docker run -ti --rm zkguard --dev
```

### Production

If you want to build the docker image compiled for production you can add the following flags:

```sh
--build-arg PROFILE=production # To enable all the optimizations
```

### Compose

Some notes about compose cluster configuration: if you want to run a node with some specific environment variables you can just edit the files in `docker/resources/envs`.
