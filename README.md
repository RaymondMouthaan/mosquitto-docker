Mosquitto-docker
================
[Eclipse Mosquitto](https://mosquitto.org) is an open source (EPL/EDL licensed) message broker that implements the MQTT protocol versions 3.1 and 3.1.1. Mosquitto is lightweight and is suitable for use on all devices from low power single board computers to full servers.

[![Build Status](https://travis-ci.org/RaymondMouthaan/mosquitto-docker.svg?branch=v1.4.15-r0)](https://travis-ci.org/RaymondMouthaan/mosquitto-docker)
[![This image on DockerHub](https://img.shields.io/docker/pulls/raymondmm/mosquitto.svg)](https://hub.docker.com/r/raymondmm/mosquitto/)

This project is inspired by [toke/mosquitto](https://github.com/toke/docker-mosquitto) docker image, but adds qemu-arm-static and uses manifest-tool to push manifest list to docker hub.

## Architectures
Currently supported archetectures:
- **linux-arm** 

## Usage
### docker run
```
docker run -d -p1883:1883 -p9001:9001 --name myMosquitto raymondmm/mosquitto
```
Exposes Port 1883 (MQTT) 9001 (Websocket MQTT)

### docker stack

```
docker stack deploy mosquitto --compose-file docker-compose-mosquitto.yml
```

Example of docker-compose.yml

```
version: "3.4"

services:
  mosquitto:
    image: raymondmm/mosquitto
    hostname: mosquitto

    networks:
      mosquitto-net:

    volumes:
      - type: bind
        source: /var/run/docker.sock
        target: /var/run/docker.sock

      - type: bind
        source: /mosquitto
        target: /mosquitto

      - type: bind
        source: /etc/localtime
        target: /etc/localtime
        read_only: true

      - type: bind
        source: /etc/timezone
        target: /etc/TZ
        read_only: true

    deploy:
      replicas: 1

networks:
  mosquitto-net:
    external: true
```

## Running with persistence


### Local directories / External Configuration

Alternatively you can use a volume to make the changes
persistent and change the configuration.

    mkdir -p /srv/mosquitto/config/
    mkdir -p /srv/mosquitto/data/
    mkdir -p /srv/mosquitto/log/
    # place your mosquitto.conf in /srv/mosquitto/config/
    # NOTE: You have to change the permissions of the directories
    # to allow the user to read/write to data and log and read from
    # config directory
    # For TESTING purposes you can use chmod -R 777 /srv/mosquitto/*
    # Better use "-u" with a valid user id on your docker host

    # Copy the files from the config directory of this project
    # into /src/mosquitto/config. Change them as needed for your
    # particular needs.

    docker run -ti -p 1883:1883 -p 9001:9001 \
    -v /srv/mosquitto:/mosquitto \
    --name mosquitto raymondmm/rpi-mosquitto

Volumes: /mosquitto

### Docker Volumes for persistence

Using [Docker Volumes](https://docs.docker.com/engine/userguide/containers/dockervolumes/) for persistence.

Create a named volume:

    docker volume create --name mosquitto_data

Now it can be attached to docker by using `-v mosquitto_data:/mosquitto` in the
Example above. Be aware that the permissions within the volumes are most likely too restrictive.

### Local time and time zone

Bind host /etc/localtime and /etc/timezone with the container too sync time and time zone:

    docker volume create ... -v /etc/localtime:/etc/localtime:ro -v /etc/timezone:/etc/TZ:ro ...

For more details check the documentation at [toke](https://github.com/toke/docker-mosquitto).
