# Dockerized XNAT for PSG
Use this repository to quickly deploy an [XNAT](https://xnat.org/) instance on [docker](https://www.docker.com/) pre-configured for use with [copla-editor](https://github.com/somnonetz/copla-editor) and [snet-plugin](https://github.com/somnonetz/snet-plugin)

## Introduction

This is a fork of [xnat-docker-compose](https://github.com/NrgXnat/xnat-docker-compose/), see `README.original.md` for the original instructions. Bellow are set up instructions for using this with copla-editor and snet-plugin

## Usage

## prerequisites

* git
* docker
* docker-compose
* npm
* gradle

### get the code

* grab `xnat-docker-compose`, `copla-editor` and `snet-plugin`

```
git clone https://github.com/somnonetz/xnat-docker-compose
git clone https://github.com/somnonetz/copla-editor
git clone https://github.com/somnonetz/snet-plugin
``` 

### set up copla-editor

* `cd copla-editor/sn-editor/`
* `git checkout xnat17`
* create the file `src/config.js` and add the following

```
const autologin = false;

const host = 'http://localhost/xnat/REST';

const credentials = {
  username: 'admin',
  password: 'admin',
};

const defaultProject = 'project1';
const defaultSubject = 'subject1';

export { autologin, host, credentials, defaultProject, defaultSubject };
```

* `npm install && npm run build`
* `cd ..`

### build snet-plugin

```
cd snet-plugin/
./gradlew jar
cp build/libs/snet01-plugin-1.0.0.jar ../xnat-docker-compose/xnat/plugins
cd ..
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
* go to `Administer -> Data Types -> Set Up Additional Data types` and add all the `snet01:*` data-types with default configuration values
* go to `Administer -> Site Administration -> Security` and disable CSRF tokens

### finished!

* you should now be able to access `xnat` at `http://localhost/xnat` and `copla-editor` at `http://localhost/copla-editor/`
