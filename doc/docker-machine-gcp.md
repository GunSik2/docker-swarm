docker-machine on Google Cloud Plaform(GCP)
=====

## Objective
How to install and run docker-machine on GCP?

## Installation
- Pre-requisite for using GCP 
  - Sign up GCP and login
  - Create a new project
  - Go to API manager and enable Google Compute Engine API
  - Download and install gcloud cli
- Login gcloud
```
$ gcloud auth login
$ gcloud auth application-default login
```
- Launch default docker-machine : f1-micro (1cpu, 0.6g mem)
```
$ docker-machine create --driver google --google-project linkideareal vm01
```
- Lauch custom docker-machine : n1-standard-1 (1cpu, 3.75g mem)
```
$ docker-machine create --driver google --google-project linkideareal --google-machine-type n1-standard-1 node01
$ docker-machine create --driver google --google-project linkideareal --google-machine-type n1-standard-1 node02
$ docker-machine create --driver google --google-project linkideareal --google-machine-type n1-standard-1 node03  
```
- Enable to run docker command without sudo. relogin required.
```
docker-machine ssh node01; sudo usermod -aG docker $USER; logout
docker-machine ssh node01; docker ps -a

docker-machine ssh node02; sudo usermod -aG docker $USER
docker-machine ssh node03; sudo usermod -aG docker $USER
```

## gcloud cmds
- image list
```
gcloud compute images list
gcloud compute images list --uri
```

## Options
```
$ docker-machine create --driver google -h
Usage: docker-machine create [OPTIONS] [arg...]

Create a machine

Description:
   Run 'C:\Program Files\cygwin64\home\user\bin\docker-machine.exe create --driver name' to include the create flags for that driver in the help text.

Options:

   --driver, -d "none"                                                                                                                  Driver to create machine with.
   --engine-env [--engine-env option --engine-env option]                                                                               Specify environment variables to set in the engine
   --engine-insecure-registry [--engine-insecure-registry option --engine-insecure-registry option]                                     Specify insecure registries to allow with the created engine
   --engine-install-url "https://get.docker.com"                                                                                        Custom URL to use for engine installation [$MACHINE_DOCKER_INSTALL_URL]
   --engine-label [--engine-label option --engine-label option]                                                                         Specify labels for the created engine
   --engine-opt [--engine-opt option --engine-opt option]                                                                               Specify arbitrary flags to include with the created engine in the form flag=value
   --engine-registry-mirror [--engine-registry-mirror option --engine-registry-mirror option]                                           Specify registry mirrors to use
   --engine-storage-driver                                                                                                              Specify a storage driver to use with the engine
   --google-address                                                                                                                     GCE Instance External IP [$GOOGLE_ADDRESS]
   --google-disk-size "10"                                                                                                              GCE Instance Disk Size (in GB) [$GOOGLE_DISK_SIZE]
   --google-disk-type "pd-standard"                                                                                                     GCE Instance Disk type [$GOOGLE_DISK_TYPE]
   --google-machine-image "https://www.googleapis.com/compute/v1/projects/ubuntu-os-cloud/global/images/ubuntu-1510-wily-v20151114"     GCE Machine Image Absolute URL [$GOOGLE_MACHINE_IMAGE]
   --google-machine-type "n1-standard-1"                                                                                                GCE Machine Type [$GOOGLE_MACHINE_TYPE]
   --google-preemptible                                                                                                                 GCE Instance Preemptibility [$GOOGLE_PREEMPTIBLE]
   --google-project                                                                                                                     GCE Project [$GOOGLE_PROJECT]
   --google-scopes "https://www.googleapis.com/auth/devstorage.read_only,https://www.googleapis.com/auth/logging.write"                 GCE Scopes (comma-separated if multiple scopes) [$GOOGLE_SCOPES]
   --google-tags                                                                                                                        GCE Instance Tags (comma-separated) [$GOOGLE_TAGS]
   --google-use-existing                                                                                                                Don't create a new VM, use an existing one [$GOOGLE_USE_EXISTING]
   --google-use-internal-ip                                                                                                             Use internal GCE Instance IP rather than public one [$GOOGLE_USE_INTERNAL_IP]
   --google-username "docker-user"                                                                                                      GCE User Name [$GOOGLE_USERNAME]
   --google-zone "us-central1-a"                                                                                                        GCE Zone [$GOOGLE_ZONE]
   --swarm                                                                                                                              Configure Machine with Swarm
   --swarm-addr                                                                                                                         addr to advertise for Swarm (default: detect and use the machine IP)
   --swarm-discovery                                                                                                                    Discovery service to use with Swarm
   --swarm-host "tcp://0.0.0.0:3376"                                                                                                    ip/socket to listen on for Swarm master
   --swarm-image "swarm:latest"                                                                                                         Specify Docker image to use for Swarm [$MACHINE_SWARM_IMAGE]
   --swarm-master                                                                                                                       Configure Machine to be a Swarm master
   --swarm-opt [--swarm-opt option --swarm-opt option]                                                                                  Define arbitrary flags for swarm
   --swarm-strategy "spread"                                                                                                            Define a default scheduling strategy for Swarm
   --tls-san [--tls-san option --tls-san option]                                                                                        Support extra SANs for TLS certs

```

## Reference
- GCP machine types: https://cloud.google.com/compute/docs/machine-types
