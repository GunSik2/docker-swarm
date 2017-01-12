Docker machine deployment
===================

## Objective 
How to deploy and manage three nodes(virtual machine) using docker machine?

## Concept
Docker Machine is a tool for provisioning and managing your Dockerized hosts (hosts with Docker Engine on them).
Docker (or Docker Engine) is the client-server application made up of the Docker daemon, a REST API that specifies interfaces for interacting with the daemon, and a command line interface (CLI) client that talks to the daemon (through the REST API wrapper).

- Docker Engine

![Docker Engine](https://docs.docker.com/machine/img/engine.png)

- Docker Machine

![Docker Machine](https://docs.docker.com/machine/img/machine.png)

## Provisioning three nodes
Assume to deploy on openstack.

### Installation 
- Install docker-machine
```
curl -L https://github.com/docker/machine/releases/download/v0.8.2/docker-machine-`uname -s`-`uname -m` \
    > /usr/local/bin/docker-machine
chmod +x /usr/local/bin/docker-machine
docker-machine version
```

### Provisioning nodes
- Configure docker-machine
```
vi openstack.rc
OS_AUTH_URL=<value>
OS_TENANT_ID=<value>
OS_TENANT_NAME=<value>
OS_USERNAME=<value>
OS_PASSWORD=<value>

vi dmachine.env
. openstack.rc
export OS_IMAGE_ID=5edf9486-e3b0-445e-a1d2-525313e1bdc1   #nova image-list
export OS_FLAVOR_ID=3                                     #nova flavor-list
export OS_NETWORK_ID=51d1d3b5-d972-453f-88a6-6ebe38fe4dbf #nova net-list   
export OS_SSH_USER=ubuntu
export OS_SECURITY_GROUPS=docker                          #nova secgroup-list
```

- Provision nodes
  - --openstack-availability-zone: OpenStack availability zone 
  - --openstack-floatingip-pool: OpenStack floating IP pool to get an IP from to assign to the instance (#nova floating-ip-pool-list)
```
. dmachine.env

docker-machine create -d openstack node1 --openstack-availability-zone Zone1 --openstack-floatingip-pool PN_PUB
docker-machine create -d openstack node2 --openstack-availability-zone Zone2
docker-machine create -d openstack node3 --openstack-availability-zone Zone3 
docker-machine ls
```

- Enable run docker command with ubuntu user 
```
docker-machine ssh node1; sudo usermod -aG docker $USER
docker-machine ssh node2; sudo usermod -aG docker $USER
docker-machine ssh node3; sudo usermod -aG docker $USER
```

## Docker-machine options
### Docker-machine openstack options
```
$ docker-machine create -d openstack -h
Usage: docker-machine create [OPTIONS] [arg...]

Create a machine

Description:
   Run 'docker-machine create --driver name' to include the create flags for that driver in the help text.

Options:

   --driver, -d "none"                                                                                  Driver to create machine with. [$MACHINE_DRIVER]
   --engine-env [--engine-env option --engine-env option]                                               Specify environment variables to set in the engine
   --engine-insecure-registry [--engine-insecure-registry option --engine-insecure-registry option]     Specify insecure registries to allow with the created engine
   --engine-install-url "https://get.docker.com"                                                        Custom URL to use for engine installation [$MACHINE_DOCKER_INSTALL_URL]
   --engine-label [--engine-label option --engine-label option]                                         Specify labels for the created engine
   --engine-opt [--engine-opt option --engine-opt option]                                               Specify arbitrary flags to include with the created engine in the form flag=value
   --engine-registry-mirror [--engine-registry-mirror option --engine-registry-mirror option]           Specify registry mirrors to use [$ENGINE_REGISTRY_MIRROR]
   --engine-storage-driver                                                                              Specify a storage driver to use with the engine
   --openstack-active-timeout "200"                                                                     OpenStack active timeout [$OS_ACTIVE_TIMEOUT]
   --openstack-auth-url                                                                                 OpenStack authentication URL [$OS_AUTH_URL]
   --openstack-availability-zone                                                                        OpenStack availability zone [$OS_AVAILABILITY_ZONE]
   --openstack-domain-id                                                                                OpenStack domain ID (identity v3 only) [$OS_DOMAIN_ID]
   --openstack-domain-name                                                                              OpenStack domain name (identity v3 only) [$OS_DOMAIN_NAME]
   --openstack-endpoint-type                                                                            OpenStack endpoint type (adminURL, internalURL or publicURL) [$OS_ENDPOINT_TYPE]
   --openstack-flavor-id                                                                                OpenStack flavor id to use for the instance [$OS_FLAVOR_ID]
   --openstack-flavor-name                                                                              OpenStack flavor name to use for the instance [$OS_FLAVOR_NAME]
   --openstack-floatingip-pool                                                                          OpenStack floating IP pool to get an IP from to assign to the instance [$OS_FLOATINGIP_POOL]
   --openstack-image-id                                                                                 OpenStack image id to use for the instance [$OS_IMAGE_ID]
   --openstack-image-name                                                                               OpenStack image name to use for the instance [$OS_IMAGE_NAME]
   --openstack-insecure                                                                                 Disable TLS credential checking. [$OS_INSECURE]
   --openstack-ip-version "4"                                                                           OpenStack version of IP address assigned for the machine [$OS_IP_VERSION]
   --openstack-keypair-name                                                                             OpenStack keypair to use to SSH to the instance [$OS_KEYPAIR_NAME]
   --openstack-net-id                                                                                   OpenStack network id the machine will be connected on [$OS_NETWORK_ID]
   --openstack-net-name                                                                                 OpenStack network name the machine will be connected on [$OS_NETWORK_NAME]
   --openstack-nova-network                                                                             Use the nova networking services instead of neutron. [$OS_NOVA_NETWORK]
   --openstack-password                                                                                 OpenStack password [$OS_PASSWORD]
   --openstack-private-key-file                                                                         Private keyfile to use for SSH (absolute path) [$OS_PRIVATE_KEY_FILE]
   --openstack-region                                                                                   OpenStack region name [$OS_REGION_NAME]
   --openstack-sec-groups                                                                               OpenStack comma separated security groups for the machine [$OS_SECURITY_GROUPS]
   --openstack-ssh-port "22"                                                                            OpenStack SSH port [$OS_SSH_PORT]
   --openstack-ssh-user "root"                                                                          OpenStack SSH user [$OS_SSH_USER]
   --openstack-tenant-id                                                                                OpenStack tenant id [$OS_TENANT_ID]
   --openstack-tenant-name                                                                              OpenStack tenant name [$OS_TENANT_NAME]
   --openstack-user-data-file                                                                           File containing an openstack userdata script [$OS_USER_DATA_FILE]
   --openstack-username                                                                                 OpenStack username [$OS_USERNAME]
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

### dcoker-machine options
```
$ docker-machine -h
Usage: docker-machine [OPTIONS] COMMAND [arg...]

Create and manage machines running Docker.

Version: 0.8.2, build e18a919

Author:
  Docker Machine Contributors - <https://github.com/docker/machine>

Options:
  --debug, -D                                                   Enable debug mode
  --storage-path, -s "/home/external/stg/.docker/machine"       Configures storage path [$MACHINE_STORAGE_PATH]
  --tls-ca-cert                                                 CA to verify remotes against [$MACHINE_TLS_CA_CERT]
  --tls-ca-key                                                  Private key to generate certificates [$MACHINE_TLS_CA_KEY]
  --tls-client-cert                                             Client cert to use for TLS [$MACHINE_TLS_CLIENT_CERT]
  --tls-client-key                                              Private key used in client TLS auth [$MACHINE_TLS_CLIENT_KEY]
  --github-api-token                                            Token to use for requests to the Github API [$MACHINE_GITHUB_API_TOKEN]
  --native-ssh                                                  Use the native (Go-based) SSH implementation. [$MACHINE_NATIVE_SSH]
  --bugsnag-api-token                                           BugSnag API token for crash reporting [$MACHINE_BUGSNAG_API_TOKEN]
  --help, -h                                                    show help
  --version, -v                                                 print the version

Commands:
  active                Print which machine is active
  config                Print the connection config for machine
  create                Create a machine
  env                   Display the commands to set up the environment for the Docker client
  inspect               Inspect information about a machine
  ip                    Get the IP address of a machine
  kill                  Kill a machine
  ls                    List machines
  provision             Re-provision existing machines
  regenerate-certs      Regenerate TLS Certificates for a machine
  restart               Restart a machine
  rm                    Remove a machine
  ssh                   Log into or run a command on a machine with SSH.
  scp                   Copy files between machines
  start                 Start a machine
  status                Get the status of a machine
  stop                  Stop a machine
  upgrade               Upgrade a machine to the latest version of Docker
  url                   Get the URL of a machine
  version               Show the Docker Machine version or a machine docker version
  help                  Shows a list of commands or help for one command
````

## Reference
- https://docs.docker.com/machine/

