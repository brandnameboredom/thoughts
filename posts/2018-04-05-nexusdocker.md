---
title: Running a local Docker registry in Nexus
description: Running a local Docker registry in Nexus
date: 2018-04-05
tags:
  - Docker
  - Nexus
---

I decided to try my hand at running my own locally hosted docker registry.  After some quick research, I settled upon [Sonatype]'s Nexus.  Now, how to get it up and running within a [Docker] container?  Let's start with a docker-compose.yaml file.  I don't necessarily care about a particular version so let's pull the [latest Nexus 3 image from Sonatype via Docker hub].

``` yaml
nexus:
    image: sonatype/nexus3:latest
```

Of course, we'll need to expose the Nexus ports and map them to ports on the host machine so let's add that definition to the docker-compose yaml file.  We'll need both the Nexus manager port (8081) as well as the port we plan to use when we setup the hosted docker repository within Nexus (8888).

``` yaml
nexus:
    image: sonatype/nexus3:latest
    ports:
        - 8081:8081
        - 8888:8888
```

That should be all we need to get everything up and running but I'm going to add an additional step and map the nexus-data directory to a directory on my host machine in case I decide to bring this container down and back up later.  This leaves us with:

``` yaml
nexus:
    image: sonatype/nexus3:latest
    ports:
        - 8081:8081
        - 8888:8888
    volumes:
        - ~/nexus-data-on-host:/nexus-data
```

Now that we have the docker-compose.yaml file, let's spin up the container.

``` zsh
docker-compose up
```

We could have run that in detached mode with a "-d" option but I wanted to monitor the logs and wait for Nexus to report it is up and running.

``` zsh
Started Sonatype Nexus OSS 3.9.0-01
```

Success!  We have Nexus up and running.  Now let's log into localhost:8081 with the default username/password combination of admin/admin123 and setup a hosted docker repository on the port we exposed for it in the docker-compose file (8888).  If your looking for instructions on how to do this consult the [Nexus documentation] on Sonatype's site.  Once we have that registry created let's log into the registry with

``` zsh
docker login http://localhost:8888/repository/docker-hosted/
```

Finally, let's tag an image we have locally and push it to our registry

``` zsh
docker tag audiojak/lightning localhost:8888/audiojak:latest

docker push localhost:8888/audiojak:latest
```

[Docker]: https://www.docker.com/
[Sonatype]: https://www.sonatype.com/
[Nexus documentation]: http://books.sonatype.com/nexus-book/3.0/reference/docker.html
[latest Nexus 3 image from Sonatype via Docker hub]: https://hub.docker.com/r/sonatype/nexus3
