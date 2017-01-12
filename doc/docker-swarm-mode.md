Docker swarm mode
===============

## Objective
What's the swarm mode and how to use it to create docker cluster?


## Concept
- Docker Swarm is native clustering for Docker. It turns a pool of Docker hosts into a single, virtual Docker host.
- Docker Engine 1.12 includes swarm mode for natively managing a cluster of Docker Engines called a swarm.
- If you’re using a Docker version prior to v1.12.0, see [Docker Swarm](https://docs.docker.com/swarm/)
- Features
  - Cluster management integrated with Docker Engine
  - Decentralized design
  - Scaling
  - Desired state reconciliation
  - Multi-host networking
  - Service discovery
  - Load balancing
  - Secure by default
  - Rolling updates

## Running swarm cluster
### Creating docker nodes
We assume that three nodes are running to create swarm cluster. If not prepared, refer to [the link](docker-machine.md) 
The three nodes are named node1, node2, node3.
### Create a swarm
- Create a new swarm
  - --advertise-addr flag configures the manager node to publish its address.
  - The other nodes in the swarm must be able to access the manager at the IP address.
```
docker-machine ssh node1
docker swarm init --advertise-addr 10.101.0.255
```
- View the current state of the swarm
```
docker info
docker node ls
```
### Add nodes to the swarm
- retrieve the join command for a worker
```
docker-machine ssh node1
docker swarm join-token worker
```
- Run the command produced 
```
docker-machine ssh node2 
docker swarm join --token SWMTKN-1-59blm3t66ip3hgy7lvhjkh4c3kmrhyuzepqlvooyp4jqfvdxgf-bpht19gdj1k4f9ozegwowf87e 10.101.0.255:2377

docker-machine ssh node3 
docker swarm join --token SWMTKN-1-59blm3t66ip3hgy7lvhjkh4c3kmrhyuzepqlvooyp4jqfvdxgf-bpht19gdj1k4f9ozegwowf87e 10.101.0.255:2377
```
- View the current state of the swarm
```
docker node ls
```

### Manage a service 
- deploy service
```
$ docker service create --replicas 1 --name helloworld alpine ping docker.com
$ docker service ps 
```
- inspect service
```
$ docker service inspect helloworld
```
- scale service
```
$ docker service scale helloworld=5
$ docker service ps helloworld
```
- delete service
```
$ docker service rm helloworld
```
- apply rolling updates
```
$ docker service create --replicas 3 --name redis --update-delay 10s redis:3.0.6
$ docker service inspect --pretty redis

$ docker service update --image redis:3.0.7 redis
$ docker service inspect --pretty redis
$ docker service ps redis
```
- drain a node
  - inactivate a node
```
$ docker node update --availability drain node2
$ docker node inspect --pretty node2
Status:
 State:			Ready
 Availability:		Drain
```
  - reactivate a node
```
$ docker node update --availability active node1
$ docker node inspect --pretty node2
Status:
 State:			Ready
 Availability:		Active
```
### routing mesh
 The routing mesh enables each node in the swarm to accept connections on published ports for any service running in the swarm, even if there’s no task running on the node. 
 The routing mesh routes all incoming requests to published ports on available nodes to an active container.
 In order to use the ingress network in the swarm, you need to have the following ports open between the swarm nodes before you enable swarm mode:
 - Port 7946 TCP/UDP for container network discovery.
 - Port 4789 UDP for the container ingress network.
 
- Publish a port for a service
  - Use the --publish flag to publish a port when you create a service
```
$ docker service create --name my-web --publish 8080:80 --replicas 2 nginx
```
  - View the service’s published port
```
$ docker service inspect --format="" my-web
[{"Protocol":"tcp","TargetPort":80,"PublishedPort":8080}]
```
  - Ingress network

![Ingress network](https://docs.docker.com/engine/swarm/images/ingress-routing-mesh.png)

- Configure an external load balancer
  - configure haproxy: /etc/haproxy/haproxy.cfg
```
global
        log /dev/log    local0
        log /dev/log    local1 notice
...snip...

# Configure HAProxy to listen on port 80
frontend http_front
   bind *:80
   stats uri /haproxy?stats
   default_backend http_back

# Configure HAProxy to route requests to swarm nodes on port 8080
backend http_back
   balance roundrobin
   server node1 192.168.99.100:8080 check
   server node2 192.168.99.101:8080 check
   server node3 192.168.99.102:8080 check
```
  - Ingress network

![Ingress network](https://docs.docker.com/engine/swarm/images/ingress-lb.png)

## Reference
- https://docs.docker.com/engine/swarm/
