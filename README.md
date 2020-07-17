# elasticsearch-docker
Mini memory version of  Elasticsearch Docker image.

Deploy your Elasticsearch container with less than 1GB memory!

Dockerhub: <https://hub.docker.com/r/mashirozx/elasticsearch-docker>

Official docker image: <https://github.com/elastic/dockerfiles/tree/7.8/elasticsearch>

## Usage

Just the same as official: <https://www.elastic.co/guide/en/elasticsearch/reference/7.8/docker.html>

### Starting a single node cluster with Docker

```bash
docker run -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" mashirozx/elasticsearch-docker:v7.8.0
```

### Starting a multi-node cluster with Docker Compose

```yml
version: '2.2'
services:
  es01:
    image: mashirozx/elasticsearch-docker:v7.8.0
    container_name: es01
    environment:
      - node.name=es01
      - cluster.name=es-docker-cluster
      - discovery.seed_hosts=es02,es03
      - cluster.initial_master_nodes=es01,es02,es03
      - bootstrap.memory_lock=true
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    ulimits:
      memlock:
        soft: -1
        hard: -1
    volumes:
      - data01:/usr/share/elasticsearch/data
    ports:
      - 9200:9200
    networks:
      - elastic
  es02:
    image: mashirozx/elasticsearch-docker:v7.8.0
    container_name: es02
    environment:
      - node.name=es02
      - cluster.name=es-docker-cluster
      - discovery.seed_hosts=es01,es03
      - cluster.initial_master_nodes=es01,es02,es03
      - bootstrap.memory_lock=true
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    ulimits:
      memlock:
        soft: -1
        hard: -1
    volumes:
      - data02:/usr/share/elasticsearch/data
    networks:
      - elastic
  es03:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.8.0
    container_name: es03
    environment:
      - node.name=es03
      - cluster.name=es-docker-cluster
      - discovery.seed_hosts=es01,es02
      - cluster.initial_master_nodes=es01,es02,es03
      - bootstrap.memory_lock=true
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    ulimits:
      memlock:
        soft: -1
        hard: -1
    volumes:
      - data03:/usr/share/elasticsearch/data
    networks:
      - elastic

volumes:
  data01:
    driver: local
  data02:
    driver: local
  data03:
    driver: local

networks:
  elastic:
    driver: bridge
```
