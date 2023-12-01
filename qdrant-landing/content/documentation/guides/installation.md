---
title: Installation
weight: 10
aliases:
  - ../install
  - ../installation
---

## Installation requirements

### CPU and memory

The amount of CPU and memory that Qdrant needs depends on the amount of vectors, their dimensions, the additional payload data that you store, and their indexes, as well as storage, replication and quantization configuration.

Our [Cloud Pricing Calculator](https://cloud.qdrant.io/calculator) can give you a very rough approximation of the necessary resources to store the vectors without any payload or index data.

### Storage

For persistent storage, Qdrant requires a block storage device with a proper file system such es ext4. This is especially important, if you mount an external volume inside the Qdrant container or if you use a Kubernetes Persistent Volume.
Qdrant won't work on a file storage device, such as NFS, or an object storage such as S3.

The amount of storage depends on the size of your data.

If you offload vectors to disk, you also have to ensure that your disk storage is performant. You should at least use SSDs or NVMEs. HDDs are not recommended.

### Networking

Each Qdrant instance opens 3 ports:

* `6333` - For the HTTP API, including the health and metric endpoints
* `6334` - For the gRPC API
* `6335` - For internal replication communication

All Qdrant instances in a cluster must be able to communicate with each other over these ports.

Each Qdrant instance must also allow incoming connections to ports `6333` and `6334` from the clients that will be using Qdrant.

## Installation options

Qdrant can be installed in different ways depending on your needs:

For testing or development setups, you can run the Qdrant container or as a binary executable. 

For production, you can use our Qdrant Cloud to run Qdrant either fully managed in our infrastructure or with Hybrid SaaS in yours. 

If you want to run Qdrant in your own infrastructure, without any cloud connection, we recommend to install Qdrant in a Kubernetes cluster with our Helm chart, or to use our Qdrant Enterprise Operator

## Production

### Qdrant Cloud

The easiest way to run Qdrant for a production setup is to use the [Qdrant Cloud](https://qdrant.to/cloud), which provides fully managed Qdrant databases. Out-of-the-box you will get easy horizontal and vertical scaling, one click installation and upgrades, monitoring, logging and backup and disaster recovery. For more information, see the [Qdrant Cloud documentention](../../cloud).

### Kubernetes

You can use a ready-made [Helm Chart](https://helm.sh/docs/) to run Qdrant in your Kubeternetes cluster.

```bash
helm repo add qdrant https://qdrant.to/helm
helm install qdrant qdrant/qdrant
```

Read further instructions at [qdrant-helm](https://github.com/qdrant/qdrant-helm/tree/main/charts/qdrant).

### Qdrant Operator

We provide a Qdrant Enterprise Operator for Kubernetes installations. If you are interested in using it, please [contact us](https://qdrant.to/contact-us).

## Development

### Docker

The easiest way to start using Qdrant for testing or development is to run the Qdrant container image.
The latest versions are always available on [DockerHub](https://hub.docker.com/r/qdrant/qdrant/tags?page=1&ordering=last_updated).

Make sure that [Docker](https://docs.docker.com/engine/install/), [Podman](https://podman.io/docs/installation) or the container runtime of your choice is installed and running. The following instructions use Docker.

Pull the image:

```bash
docker pull qdrant/qdrant
```

Run the container:

```bash
docker run -p 6333:6333 \
    -v $(pwd)/path/to/data:/qdrant/storage \
    qdrant/qdrant
```

With this command, you will start a Qdrant instance with the default configuration.
It will store all data in the `./path/to/data` directory.

By default, Qdrant uses port 6333, so at [localhost:6333](http://localhost:6333) you should see the welcome message.

To change the Qdrant configuration, you can overwrite the production configuration:

```bash
docker run -p 6333:6333 \
    -v $(pwd)/path/to/data:/qdrant/storage \
    -v $(pwd)/path/to/custom_config.yaml:/qdrant/config/production.yaml \
    qdrant/qdrant
```

Or use your own configuration file and specify it:

```bash
docker run -p 6333:6333 \
    -v $(pwd)/path/to/data:/qdrant/storage \
    -v $(pwd)/path/to/custom_config.yaml:/qdrant/config/custom_config.yaml \
    qdrant/qdrant \
    ./qdrant --config-path config/custom_config.yaml
```

For more details see the [Configuration](../configuration) section.

### Docker Compose

You can also use [Docker Compose](https://docs.docker.com/compose/) to run Qdrant.

Here is an example compose file for a single node Qdrant cluster with custom configuration:

```yaml
services:
  qdrant:
    image: qdrant/qdrant:v1.6.1
    restart: always
    container_name: qdrant
    ports:
      - 6333:6333
      - 6334:6334
    expose:
      - 6333
      - 6334
      - 6335
    configs:
      - source: qdrant_config
        target: /qdrant/config/production.yaml
    volumes:
      - ./qdrant_data:/qdrant_data

configs:
  qdrant_config:
    content: |
      log_level: INFO  
```

### From source

Qdrant is written in Rust and can be compiled into a binary executable.
This installation method can be helpful if you want to compile Qdrant for a specific processor architecture or if you do not want to use Docker for some reason.

Before compiling, make sure that the necessary libraries and the [rust toolchain](https://www.rust-lang.org/tools/install) are installed.
The current list of required libraries can be found in the [Dockerfile](https://github.com/qdrant/qdrant/blob/master/Dockerfile).

Build Qdrant with Cargo:

```bash
cargo build --release --bin qdrant
```

After a successful build, the binary is available at `./target/release/qdrant`.

## Client libraries

In addition to the service itself, Qdrant provides client libraries for different programming languages. For a full list and documentation, see the [Client libraries](../../interfaces/#client-libraries) section.
