Running a service in docker swarm mode with persistent store
=======

## Postgres without persistency
- Run postgres service
```
eval $(docker-machine env node1)
docker service create \
  --env POSTGRES_DB=postres \
  --env POSTGRES_USER=postres \
  --env POSTGRES_PASSWORD=postres \
  --publish 5432:5432 \
  --name postgres \
  postgres:9.5
```

- Test postgres
```
docker run \
  --add-host postgres:$(docker-machine ip node1) \
  --env PGPASSWORD=postgres \
  -it --rm postgres:9.5 \
  psql -h postgres -U postgres
#   
```

- Stop postgres
```
docker service rm postgres
```

- Run postgres service again
- Test postgres again
  - Check that the created tables are not persisted.
  
## Postgres with persistency

