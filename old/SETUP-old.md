## About

This setup uses a Digital Ocean droplet to host the Ladder99 pipeline (Relay, Postgres, Grafana), SQL access (Hue), and a reverse proxy (Traefik). The Traefik container will be the only one with port 80 and 443 exposed.

## Diagram source

https://docs.google.com/drawings/d/1bBneF6XSqm4Qls_OiLGLMFbBTbfJwSQ-BV3SmlJ-H90/edit?usp=sharing

## Setup

Login to the Droplet console here -
https://cloud.digitalocean.com/droplets/285467341/access

Login as root then drop down to user with `su - user`.

Make a directory ladder99

    mkdir ladder99
    cd ladder99

Clone this repo and ladder99-ce there

    git clone https://github.com/Ladder99/ladder99-ce
    git clone https://github.com/Ladder99/client-mazak
    cd ladder99-ce
    git checkout develop

Start all Docker services (Grafana, Hue, Postgres, Relay, Traefik) -

    ./start mazak all

- you'll be asked to edit an .env file - be sure to set the Postgres password, 
and add the MTConnect Agents you want to query - e.g. 

    AGENT_ENDPOINTS=http://mtconnect.mazakcorp.com:5717


## Status

See status of services with

    docker ps

## Postgres

To access the database console

    docker exec -it postgres bash

then within the postgres container

    psql -U postgres

now can enter SQL and Postgres commands

    psql (13.3)
    Type "help" for help.
    postgres=#

eg

    postgres=# \d
                    List of relations
    Schema |       Name        |   Type   |  Owner
    --------+-------------------+----------+----------
    public | bins              | table    | postgres
    public | dataitems         | view     | postgres
    public | devices           | view     | postgres
    public | edges             | table    | postgres
    public | history           | table    | postgres
    public | history_all       | view     | postgres
    public | history_float     | view     | postgres
    public | history_text      | view     | postgres
    public | meta              | table    | postgres
    public | metrics           | view     | postgres
    public | nodes             | table    | postgres
    public | nodes_node_id_seq | sequence | postgres

## Update

To update code when github repos are updated

    cd ~/ladder99/ladder99-ce
    ./update mazak

this will update both ladder99-ce and client-mazak repos.

If you need to update the running Grafana dashboard,

    docker stop grafana
    ./start mazak grafana

To restart the Relay service,

    docker stop relay
    ./start mazak relay

## Source

This setup is based on
https://www.digitalocean.com/community/tutorials/how-to-use-traefik-v2-as-a-reverse-proxy-for-docker-containers-on-ubuntu-20-04
