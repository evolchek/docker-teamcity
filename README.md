[TeamCity](https://www.jetbrains.com/teamcity/) is a continuous integration server from [JetBrains](https://www.jetbrains.com/).

## How to use the image

It is recommended to use a data container to persist the application data:

```
docker run --name teamcity-data \
  -v /var/lib/teamcity -v /var/lib/postgresql/data \
  busybox /bin/false
```

If you're not planning to use an external database, just omit the Postgres volume.

However it is strongly recommended to use an external database on production installations. You can start a Postgres container for a dedicated external DB:

```
docker run --name teamcity-db -d \
  -e POSTGRES_USER=teamcity -e POSTGRES_PASSWORD=somepass \
  --volumes-from teamcity-data postgres
```

Now run the TeamCity container:

```
docker run -d --name teamcity --volumes-from teamcity-data \
  --link teamcity-db:teamcity-db -p 8111:8111 klikatech/teamcity
```

If you didn't start the Postgres container on the previous step, simply omit the --link argument.

In order to run build jobs you need to start at least one build agent. See [teamcity-agent-base](https://registry.hub.docker.com/u/klikatech/teamcity-agent-base/) image for details.

### Staying up to date

In order to update the TeamCity installation to latest version you need to stop the container, pull the latest version of the image and start a new container:

```
docker stop teamcity
docker rm teamcity
docker run -d --name teamcity --volumes-from teamcity-data \
  --link teamcity-db:teamcity-db -p 8111:8111 klikatech/teamcity
```
