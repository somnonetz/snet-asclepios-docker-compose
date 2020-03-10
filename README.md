# Dockerized XNAT for Polysomnography

Use this repository to quickly deploy an [XNAT](https://xnat.org/) instance for storing polysomnographies on [docker](https://www.docker.com/). Pre-configured for use with [copla-editor](https://github.com/somnonetz/copla-editor) and [snet-plugin](https://github.com/somnonetz/snet-plugin)

## Introduction

This is a fork of [xnat-docker-compose](https://github.com/NrgXnat/xnat-docker-compose/), see `README.original.md` for the original instructions. Bellow are set up instructions for using this with copla-editor and snet-plugin

## Usage

## prerequisites

* git
* docker
* docker-compose

### get the code

* grab `xnat-docker-compose`

```
git clone https://github.com/somnonetz/xnat-docker-compose
``` 

### run services with xnat-docker-compose

```
cd xnat-docker-compose/
docker-compose build
docker-compose up -d
```

### set up xnat

* go to `http://localhost/xnat` and login
* create a project and subject matching the values in your `copla-editor` config
* go to `Administer -> Data Types -> Set Up Additional Data types` and add all the `snet01:*` data-types.
* go to `Administer -> Site Administration -> Security` and disable CSRF tokens

### finished!

* you should now be able to access `xnat` at `http://localhost/xnat` and `copla-editor` at `http://localhost/copla-editor/`

### Troubleshooting

* You can find xnat's logs on the host at: `./xnat-data/home/logs`
* To get a shell on a running `xnat-web` container and access the file system run: `docker-compose exec xnat-web bash`
