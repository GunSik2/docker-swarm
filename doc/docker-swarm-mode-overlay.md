Docker swarm mode with overlay network
=====

## Objective
How to use overlay network in docker swarm mode?

## Concept
Docker Engine swarm mode natively supports overlay networks, so you can enable container-to-container networks.
Features of swarm mode overlay networks include the following:
- You can attach multiple services to the same network.
- By default, service discovery assigns a virtual IP address (VIP) and DNS entry to each service in the swarm.
- You can configure the service to use DNS round-robin instead of a VIP
In order to use overlay networks in the swarm, you need to have the following ports open between the swarm nodes before you enable swarm mode:
- Port 7946 TCP/UDP for container network discovery.
- Port 4789 UDP for the container overlay network.

## Create an overlay network in a swarm
```
$ docker network create --driver overlay --subnet 10.0.9.0/24 --opt encrypted my-network
$ docker network ls
NETWORK ID          NAME        DRIVER   SCOPE
f9145f09b38b        bridge      bridge   local
..snip..
273d53261bcd        my-network  overlay  swarm
```
## Attach a service to an overlay network
```
$ docker service create --replicas 3 --name my-web --network my-network nginx
$ docker service ps my-web
$ docker network inspect my-network
```

![my-network](https://docs.docker.com/engine/swarm/images/service-vip.png)

## Use swarm mode service discovery
- Inspect the service to view the virutal IP
```
$ docker service inspect --format='{{json .Endpoint.VirtualIPs}}' my-web
[{"NetworkID":"7m2rjx0a97n88wzr4nu8772r3" "Addr":"10.0.0.2/24"}]
```
- Add a new service on the same network, which is able to access nginx using the DNS name my-web
```
$ docker service create --name my-busybox --network my-network busybox sleep 3000
$ docker service ps my-busybox
$ docker exec -it my-busybox.1.1dok2cmx2mln5hbqve8ilnair /bin/sh
# nslookup my-web
Name:      my-web
Address 1: 10.0.9.2 ip-10-0-9-2.us-west-2.compute.internal
# nslookup tasks.my-web
Name:      tasks.my-web
Address 1: 10.0.9.4 my-web.2.6b3q2qbjveo4zauc6xig7au10.my-network
Address 2: 10.0.9.3 my-web.1.63s86gf6a0ms34mvboniev7bs.my-network
Address 3: 10.0.9.5 my-web.3.66u2hcrz0miqpc8h0y0f3v7aw.my-network
# wget -O- my-web
```

## Use DNS round-robin for a service
- configure the service to use DNS round-robin directly without using a VIP using **--endpoint-mode dnsrr** 
```
$ docker service create --replicas 3 --name my-dnsrr-service --network my-network --endpoint-mode dnsrr nginx
```
-  query the DNS for the service name inside the busybox container
```
$ nslookup my-dnsrr-service
Name:      my-dnsrr
Address 1: 10.0.9.8 my-dnsrr-service.1.bd3a67p61by5dfdkyk7kog7pr.my-network
Address 2: 10.0.9.10 my-dnsrr-service.3.0sb1jxr99bywbvzac8xyw73b1.my-network
Address 3: 10.0.9.9 my-dnsrr-service.2.am6fx47p3bropyy2dy4f8hofb.my-network
```

## Advanced configuration case
- Using HTTP Routing Mesh
  - Traffic comes in through the swarm mode routing mesh on the ingress network to the HRM service's published port.
  - As services are created, they are assigned a VIP on the swarm mode routing mesh (L4).
  - The HRM receives the TCP packet and inspects the HTTP header.
    - Services that contain the label com.docker.ucp.mesh.http are checked if they match the HTTP Host: header.
    - If a Host: header and service label label match, then traffic is routed to the service's VIP using the swarm mode routing mesh (L4).
  - If a service contains multiple replicas, then each replica container will be load balanced via round-robin using the internal L4 routing mesh.
  
![](https://success.docker.com/@api/deki/files/294/hrm-up-close.png?revision=2)


- Using proxy docker flow

![](https://leanpub.com/site_images/the-devops-2-1-toolkit/ch03----swarm-nodes-proxy-scaled.png)


## Reference
- https://docs.docker.com/engine/swarm/networking/
- http://proxy.dockerflow.com/swarm-mode-auto/
- https://leanpub.com/the-devops-2-1-toolkit/read
- https://success.docker.com/Datacenter/Apply/Docker_Reference_Architecture%3A_Universal_Control_Plane_2.0_Service_Discovery_and_Load_Balancing
