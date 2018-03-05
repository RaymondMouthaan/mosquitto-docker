mosquitto-docker
================
[![Build Status](https://travis-ci.org/RaymondMouthaan/mosquitto-docker.svg?branch=v1.4.15-r0)](https://travis-ci.org/RaymondMouthaan/mosquitto-docker)
[![This image on DockerHub](https://img.shields.io/docker/pulls/raymondmm/mosquitto.svg)](https://hub.docker.com/r/raymondmm/mosquitto/)

Docker image for Mosquitto. A lightweight MQTT broker for Raspberry Pi based on Arm Alpine Linux.
Inspired by https://github.com/toke/docker-mosquitto

## Run

    docker run -ti -p 1883:1883 -p 9001:9001 raymondmm/rpi-mosquitto

Exposes Port 1883 (MQTT) 9001 (Websocket MQTT)

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
Example above. Be aware that the permissions within the volumes
are most likely too restrictive.

### Local time and time zone

Bind host /etc/localtime and /etc/timezone with the container too sync time and time zone:

    docker volume create ... -v /etc/localtime:/etc/localtime:ro -v /etc/timezone:/etc/TZ:ro ...
