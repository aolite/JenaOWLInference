# OWL Reasoning in Apache Jena Fuseki

The main intention of the document is to make a tutorial of OWL reasoning in Apache Jena Fuseki. As a previous work performed, there has been several tutorials and documents that we used as a base: 

1. [Getting started with RDF SPARQL queries and inference using Apache Jena Fuseki](https://christinemdraper.wordpress.com/2017/04/09/getting-started-with-rdf-sparql-jena-fuseki/)
2. [Configuring Apache Jena Fuseki 2.4.1 inference and reasoning support using SPARQL 1.1: Jena inference rules, RDFS Entailment Regimes and OWL reasoning](https://github.com/jfmunozf/Jena-Fuseki-Reasoner-Inference/wiki/Configuring-Apache-Jena-Fuseki-2.4.1-inference-and-reasoning-support-using-SPARQL-1.1:-Jena-inference-rules,-RDFS-Entailment-Regimes-and-OWL-reasoning)
3. [Snippet about the configuration in Fuseki](https://gist.github.com/ruebot/fb7b1da82042860138d2d609756e07dc)

## 1. Setup Fuseki Server

The initial step is to configure Fuseki in our machine. For that purpose, we used [Docker](https://www.docker.com/). Our initial step is to define a docker-compose to deploy in our machine. For that, we need to create a document called "docker-compose.yml" in or root directory of the project: 

```
version: "3.5"
services:
    fuseki:
        image: stain/jena-fuseki
        container_name: st_fuseki
        restart: always
        ports:
            - "32768:3030"
        volumes:
            - "./volumes/fuseki_data:/fuseki"
        environment:
            ADMIN_PASSWORD: st_FuSeKi!
```

The code showed above describes the following aspects: 

* **"version"**: Determines the version of docker that in our case is "3.5". 
* **"services"**: Defines the different configurations (containers) that will compose our docker. In our case, we would like to include fuseki triplestore. 

Based on the fuseki configuration, we define the following aspects: 

* **"image"**: Represents the docker image to use. In our case, we would like to include a built docker image for Fuseki named as [stain/jena-fuseki](https://hub.docker.com/r/stain/jena-fuseki)
* **"container_name"**: Defines the name of our container. In this case, it could be any string we want. We used as a name "st_fuseki". 
* **"restart"**: It indicates the restart policy for the specified container. According to the [documentation](https://docs.docker.com/compose/compose-file/), when *"always"* is specified, the container always restarts. 
* **"ports"**: Indicates the match between the docker internal container port with the port where the docker is exposed. The initial value indicates the machine value and the second value indicates the docker port. In our case, the internal port of fuseki inside docker is "3030" and the accesible port from our machine will be the port "32768".
* **"volumes"**: Defines the data volumes that fuseki will use to store the persisted data. For our case, it is important in order to having control of the different configurations files as we will show in later points. In this regards, we matched the local "/volumes/fuseki_data" folder with the docker fuseki folder named as "/fuseki" where fuseki stores the different databases.
* **"environments"**: Defines the environment variables for the docker file. The only variable required for running fuseki is the admin password. 

Once built the docker-compose file, the next step is to build the docker-compose. For that, we created a file named as "docker-build.sh" that includes: 

```
#!/usr/bin/env bash
docker-compose -p dev -f docker-compose.yml build
```

Moreover, we also create the "docker-start.sh" (for starting the docker): 

```
#!/usr/bin/env bash
docker-compose -p dev -f docker-compose.yml up -d
```

The "docker-stop.sh" for stoping the docker: 

```
#!/usr/bin/env bash
docker-compose -p dev -f docker-compose.yml down
```

And the "docker-log.sh" for logging the "st-fuseki" container: 

```
#!/usr/bin/env bash
docker container logs -f st_fuseki
```

With all of these commands we could manage our fuseki triplestore docker. 

## 2. 