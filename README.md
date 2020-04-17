# Dockerized XNAT for Polysomnography

Use this repository to quickly deploy an [XNAT](https://xnat.org/) instance for storing polysomnographies on [docker](https://www.docker.com/). Pre-installed with [copla-editor](https://github.com/somnonetz/copla-editor), [snet-plugin](https://github.com/somnonetz/snet-plugin) and [snet-pipelines](https://github.com/somnonetz/snet-pipelines)

<p align="center">
  <img src="docker-compose.png">
</p>

## Introduction

This is a fork of [xnat-docker-compose](https://github.com/NrgXnat/xnat-docker-compose/), see `README.original.md` for the original instructions. Bellow are set up instructions for using this with `copla-editor`, `snet-plugin`, `snet-pipelines`

## Usage

If you just want to run xnat in docker with the latest stable versions of `copla-editor`, `snet-plugin` and `snet-pipelines`, set up is fairly straight forward

### Build and run

```command
git clone https://github.com/somnonetz/snet-docker-compose

cd snet-docker-compose
cp .env.example .env # edit this file to configure
mkdir -p data/nginx/conf.d
cp nginx/xnat.local.conf data/nginx/conf.d

docker-compose build
docker network create asclepios
docker-compose up -d
```

### Set up xnat

* Go to `http://localhost/xnat` and login
* Go to `Administer -> Site Administration -> Security` and disable CSRF tokens
* Go to `Administer -> Data Types -> Set Up Additional Data types` and add all the `snet01:*` data-types (for now, psgScanData needs to be set as unsecure)
* Go to `Administer -> Site Administration -> Pipeline Settings` and set the processing url to `http://xnat:8080/xnat`
* Go to `Adminster -> Pipelines -> Add pipeline to repository` and add a pipeline with the path: `/data/xnat/pipeline/catalog/somnonetz-pipeline/somnonetz-pipeline.xml`
* Create at least one project and subject
* Go to `Browse -> All Projects -> Project -> Pipelines -> Add Pipeline` and add `somononetz-pipeline`

### Finished!

* You should now be able to access `xnat` at `http://localhost/xnat` and `copla-editor` at `http://localhost/copla-editor/`

## Developing

If you want to use this as a development environment for `copla-editor`, `snet-plugin` and `snet-pipelines`, set up is a bit more involved

### Run external services

Follow the instructions in the "Usage" section above

### Get the code

* Grab `snet-docker-compose`, `xnat-pipeline-engine` `copla-editor`, `snet-plugin` and `snet-pipelines`

```command
git clone https://github.com/somnonetz/snet-docker-compose
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

const host = `${window.location.origin}/xnat/REST`;

const credentials = {};

const pipelineName = 'somnonetz-pipeline';
const pipelineParams = {};

export { autologin, host, credentials, pipelineName, pipelineParams }
```

* `npm install && npm run build`
* `cd ../..`

### Build snet-plugin

```command
cd snet-plugin/
./gradlew jar
cp build/libs/snet01-plugin-1.0.0.jar ../snet-docker-compose/xnat/plugins
cd ..
```

### Set up xnat-pipeline-engine

```command
cd xnat-pipeline-engine
cp ../snet-docker-compose/xnat/xnat-pipeline-engine-gradle.properties gradle.properties
cd ..
```
### Set up local volumes mounts

* `cd  snet-docker-compose/`
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

```command
cp .env.example .env
docker-compose build
docker-compose up -d  # wait for service to finsish booting, use `docker-compose logs -f xnat-web` to monitor
docker-compose exec xnat bash
cd /tmp/xnat-pipeline-engine
./gradlew
exit
```

### Set up xnat

Follow the instructions in the "Usage" section above

## Troubleshooting

* You can find xnat's logs on the host at: `./xnat-data/home/logs`
* To get a shell on a running `xnat` container and access the file system run: `docker-compose exec xnat bash`
