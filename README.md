# Dockerized XNAT for Polysomnography

Use this repository to quickly deploy an [XNAT](https://xnat.org/) instance for storing polysomnographies on [docker](https://www.docker.com/). Pre-installed with [copla-editor](https://github.com/somnonetz/copla-editor), [snet-plugin](https://github.com/somnonetz/snet-plugin) and [snet-pipelines](https://github.com/somnonetz/snet-pipelines)

## Introduction

This is a fork of [xnat-docker-compose](https://github.com/NrgXnat/xnat-docker-compose/), see `README.original.md` for the original instructions. Bellow are set up instructions for using this with `copla-editor`, `snet-plugin`, `snet-pipelines`

## Usage

If you just want to run xnat in docker with the latest stable versions of `copla-editor`, `snet-plugin` and `snet-pipelines`, set up is fairly straight forward

### Build and run containers

```sh
git clone https://github.com/somnonetz/snet-xnat-docker-compose
cd snet-xnat-docker-compose
cp .env.example .env # edit this file to configure
docker-compose build
docker-compose up -d
```

### Set up xnat

* Go to `http://localhost/xnat` and login
* Create a project and subject matching the values in your `copla-editor` config (by default they are `project1` and `subject1`)
* Go to `Administer -> Data Types -> Set Up Additional Data types` and add all the `snet01:*` data-types (for now, psgScanData needs to be set as unsecure)
* Go to `Administer -> Site Administration -> Security` and disable CSRF tokens
* Go to `Administer -> Site Administration -> Pipeline Settings` and set the processing url to `http://xnat-web:8080/xnat`
* Go to `Adminster -> Pipelines -> Add pipeline to repository` and add a pipeline with the path: `/data/xnat/pipeline/catalog/somnonetz-pipeline/somnonetz-pipeline.xml`

### Finished!

* You should now be able to access `xnat` at `http://localhost/xnat` and `copla-editor` at `http://localhost/copla-editor/`

## Developing

If you want to use this as a development environment for `copla-editor`, `snet-plugin` and `snet-pipelines`, set up is a bit more involved

### Get the code

* Grab `snet-xnat-docker-compose`, `xnat-pipeline-engine` `copla-editor`, `snet-plugin` and `snet-pipelines`

```sh
git clone https://github.com/somnonetz/snet-xnat-docker-compose
git clone https://github.com/NrgXnat/xnat-pipeline-engine.git
git clone https://github.com/somnonetz/copla-editor
git clone https://github.com/somnonetz/snet-plugin
git clone https://github.com/somnonetz/snet-pipelines
```

### Set up copla-editor

* `cd copla-editor/sn-editor/`
* Create the file `src/config.js` and add the following:

```js
const autologin = false;

const host = 'http://localhost/xnat/REST';

const credentials = {
  username: 'admin',
  password: 'admin',
};

const defaultProject = 'project1';
const defaultSubject = 'subject1';
const pipelineName = 'somnonetz-pipeline';
const pipelineParams = {};

export { autologin, host, credentials, defaultProject, defaultSubject, pipelineName, pipelineParams };
```

* `npm install && npm run build`
* `cd ../..`

### Build snet-plugin

```sh
cd snet-plugin/
./gradlew jar
cp build/libs/snet01-plugin-1.0.0.jar ../snet-xnat-docker-compose/xnat/plugins
cd ..
```

### Set up xnat-pipeline-engine

```sh
cd xnat-pipeline-engine
cp ../snet-xnat-docker-compose/xnat/xnat-pipeline-engine-gradle.properties gradle.properties
cd ..
```
### Set up local volumes mounts

* `cd  snet-xnat-docker-compose/`
* Edit `docker-compose.yml` and uncomment the following lines:

```yml
      # - ./xnat/plugins:/data/xnat/home/plugins
      # - ../xnat-pipeline-engine:/tmp/xnat-pipeline-engine
      # - ../snet-pipelines:/tmp/snet-pipelines
```

and

```yml
    # volumes:
    #   - ../copla-editor/sn-editor/build:/var/www/copla-editor
```

### Build and run containers and install pipelines

```sh
docker-compose build
docker-compose up -d  # wait for service to finsish booting, use docker-compose logs xnat-web to monitor
docker-compose exec xnat-web bash
cd /tmp/xnat-pipeline-engine
./gradlew
exit
```

### Set up xnat

Follow the instructions in the "Usage" section above

## Troubleshooting

* You can find xnat's logs on the host at: `./xnat-data/home/logs`
* To get a shell on a running `xnat-web` container and access the file system run: `docker-compose exec xnat-web bash`
