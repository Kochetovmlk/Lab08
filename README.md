## Lab8
nano Dockerfile-client
```
FROM ubuntu:22.10

RUN apt update && apt install curl --no-install-recommends -y

ENV SERVER_URL http://google.com

ENTRYPOINT bash -c "while true ; do sleep 5 && curl -qq $SERVER_URL; done" 
```
==========================================================
nano Dockerfile-server
```
FROM ubuntu:22.10

RUN apt update && apt install npm --no-install-recommends -y && \
    mkdir -p /opt/server

WORKDIR /opt/server

RUN npm init -y && \
    npm install json-server

COPY package.json /opt/server/package.json
COPY db.json /opt/server/db.json

EXPOSE 3000

POINT ["npm", "start"]
```
===========================================================
nano db.json
```
{
  "users": [
    {
      "id": 1,
      "name": "David",
      "age": 30
    },
    {
      "name": "John",
      "id": 2,
      "age": 40
     }
   ]
}
```
========================================================
nano package.json
```

{
  "name": "server",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "json-server": "^0.17.3"
  },
  "scripts": {
    "start": "json-server -H 0.0.0.0 -p 3000 db.json"
  }
}
```
=====================================================
nano docker-compose.yml
```
version: '3.9'

services:
  server:
    image: Lab08:server
    container_name: server
    networks:
      - Lab08-network
  client:
    image: Lab08:client
    container_name: client
    networks:
      - Lab08-network
    environment:
      - SERVER_URL=http://server:3000/users

networks:
  Lab08-network:
    driver: "bridge"
    ipam:
      driver: default
      config:
        - subnet: 172.20.0.0/16
```
====================================================
Билд и запуск:
```
docker build -f Dockerfile-server -t Lab08:server .
docker build -f Dockerfile-client -t Lab08:client .
docer-compose up -d
docker logs client
docker logs server
docker-compose down
```
