Running a service in docker swarm mode with persistent store
=======

## Preparation
- Create four node docker-machines
- Install and confiure convory and nfs
  - nfs server on a node 
  - convoy daemon on three nodes

## Postgres without persistency
- Run postgres service (node1-master)
```
eval $(docker-machine env node1)
docker service create \
  --env POSTGRES_DB=postgres \
  --env POSTGRES_USER=postgres \
  --env POSTGRES_PASSWORD=postgres \
  --publish 5432:5432 \
  --name postgres \
  postgres:9.5
```

- Test postgres (node2 or node3)
```
docker run \
  --add-host postgres:10.128.0.3 \
  --env PGPASSWORD=postgres \
  -it --rm postgres:9.5 \
  psql -h postgres -U postgres
# CREATE DATABASE testdb;
# \l
# \c testdb;
# create table company(id int primary key not null, name text not null, age int not null);
# \d
# \d company
```

- Stop postgres
```
docker service rm postgres
```

- Run postgres service again
- Test postgres again
  - Check that the created tables are not persisted.
  
## Postgres with persistency
- Run postgres service (node1-master)
```
eval $(docker-machine env node1)
docker service create \
  --mount src=/home/docker-user/nfs/vol1/postgressql/data,dst=/var/lib/postgresql/data \
  --env POSTGRES_DB=postgres \
  --env POSTGRES_USER=postgres \
  --env POSTGRES_PASSWORD=postgres \
  --publish 5432:5432 \
  --name postgres \
  postgres:9.5
#  --mount type=volume,src=vol1,volume-driver=local,dst=/kkk,volume-opt=type=nfs,volume-opt=device=10.128.0.3:/var/nfs

```

- Test postgres (node2 or node3)
```
docker run \
  --add-host postgres:10.128.0.3 \
  --env PGPASSWORD=postgres \
  -it --rm postgres:9.5 \
  psql -h postgres -U postgres
# CREATE DATABASE testdb;
# \l
# \c testdb;
# create table company(id int primary key not null, name text not null, age int not null);
# \d
# \d company
```

- Stop postgres
```
docker service rm postgres
```

- Run postgres service again
- Test postgres again
  - Check that the created tables are not persisted.
  



## Reference
- https://medined.github.io/docker/postgres/2016/08/20/running-postgres-on-docker-swarm.html
- http://info.crunchydata.com/blog/easy-postgresql-cluster-recipe-using-docker-1.12
- https://docs.docker.com/engine/swarm/services/
