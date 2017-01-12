Docker machine on CloudStack
=====

## Objective
How to deploy and mange docker machine on cloudstack?


## Install
- docker-ahcine
```
curl -L https://github.com/docker/machine/releases/download/v0.8.2/docker-machine-`uname -s`-`uname -m` > docker-machine 
chmod +x docker-machine
sudo mv docker-machine /usr/local/bin/
docker-machine version
```
- cloudstack driver
```
wget https://github.com/atsaki/docker-machine-driver-cloudstack/releases/download/v0.1.4/docker-machine-driver-cloudstack_linux_amd64
chmod +x docker-machine-driver-cloudstack_linux_amd64
sudo mv docker-machine-driver-cloudstack_linux_amd64 /usr/local/bin/docker-machine-driver-cloudstack
docker-machine create -d cloudstack --help
```

## Run
- create a config 
```
#!/bin/bash

export CLOUDSTACK_API_URL=<value>
export CLOUDSTACK_API_KEY=<value>
export CLOUDSTACK_SECRET_KEY=<value>
```
- create a new machine
```
docker-machine create -d cloudstack \
  --cloudstack-template "Ubuntu Server 14.04" \
  --cloudstack-zone "DemoZone" \
  --cloudstack-service-offering "Small" \
  --cloudstack-expunge \
  node1
```


## Help
- cloudstack driver
```
$ docker-machine create -d cloudstack --help
Usage: docker-machine create [OPTIONS] [arg...]

Create a machine

Description:
   Run 'docker-machine create --driver name' to include the create flags for that driver in the help text.

Options:

   --cloudstack-api-key                                                                                 CloudStack API key [$CLOUDSTACK_API_KEY]
   --cloudstack-api-url                                                                                 CloudStack API URL [$CLOUDSTACK_API_URL]
   --cloudstack-cidr [--cloudstack-cidr option --cloudstack-cidr option]                                Source CIDR to give access to the machine. default 0.0.0.0/0
   --cloudstack-expunge                                                                                 Whether or not to expunge the machine upon removal
   --cloudstack-http-get-only                                                                           Only use HTTP GET to execute CloudStack API [$CLOUDSTACK_HTTP_GET_ONLY]
   --cloudstack-network                                                                                 CloudStack network
   --cloudstack-public-ip                                                                               CloudStack Public IP
   --cloudstack-secret-key                                                                              CloudStack API secret key [$CLOUDSTACK_SECRET_KEY]
   --cloudstack-service-offering                                                                        CloudStack service offering
   --cloudstack-ssh-user "root"                                                                         CloudStack SSH user
   --cloudstack-template                                                                                CloudStack template
   --cloudstack-timeout "300"                                                                           time(seconds) allowed to complete async job [$CLOUDSTACK_TIMEOUT]
   --cloudstack-use-port-forward                                                                        Use port forwarding rule to access the machine
   --cloudstack-use-private-address                                                                     Use a private IP to access the machine
   --cloudstack-userdata-file                                                                           CloudStack Userdata file
   --cloudstack-zone                                                                                    CloudStack zone
   --driver, -d "none"                                                                                  Driver to create machine with. [$MACHINE_DRIVER]
   --engine-env [--engine-env option --engine-env option]                                               Specify environment variables to set in the engine
   --engine-insecure-registry [--engine-insecure-registry option --engine-insecure-registry option]     Specify insecure registries to allow with the created engine
   --engine-install-url "https://get.docker.com"                                                        Custom URL to use for engine installation [$MACHINE_DOCKER_INSTALL_URL]
   --engine-label [--engine-label option --engine-label option]                                         Specify labels for the created engine
   --engine-opt [--engine-opt option --engine-opt option]                                               Specify arbitrary flags to include with the created engine in the form flag=value
   --engine-registry-mirror [--engine-registry-mirror option --engine-registry-mirror option]           Specify registry mirrors to use [$ENGINE_REGISTRY_MIRROR]
   --engine-storage-driver                                                                              Specify a storage driver to use with the engine
   --swarm                                                                                              Configure Machine to join a Swarm cluster
   --swarm-addr                                                                                         addr to advertise for Swarm (default: detect and use the machine IP)
   --swarm-discovery                                                                                    Discovery service to use with Swarm
   --swarm-experimental                                                                                 Enable Swarm experimental features
   --swarm-host "tcp://0.0.0.0:3376"                                                                    ip/socket to listen on for Swarm master
   --swarm-image "swarm:latest"                                                                         Specify Docker image to use for Swarm [$MACHINE_SWARM_IMAGE]
   --swarm-join-opt [--swarm-join-opt option --swarm-join-opt option]                                   Define arbitrary flags for Swarm join
   --swarm-master                                                                                       Configure Machine to be a Swarm master
   --swarm-opt [--swarm-opt option --swarm-opt option]                                                  Define arbitrary flags for Swarm master
   --swarm-strategy "spread"                                                                            Define a default scheduling strategy for Swarm
   --tls-san [--tls-san option --tls-san option]                                                        Support extra SANs for TLS certs

```
