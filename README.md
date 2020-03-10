# Dockerized XNAT for Polysomnography

Use this repository to quickly deploy an [XNAT](https://xnat.org/) instance for storing polysomnographies on [docker](https://www.docker.com/). Pre-configured for use with [copla-editor](https://github.com/somnonetz/copla-editor) and [snet-plugin](https://github.com/somnonetz/snet-plugin)

## Introduction

This is a fork of [xnat-docker-compose](https://github.com/NrgXnat/xnat-docker-compose/), see `README.original.md` for the original instructions. Bellow are set up instructions for using this with `copla-editor` and `snet-plugin`

## Usage

If you just want to run xnat in docker with the latest stable versions of `copla-editor` and `snet-plugin`, set up is fairly straight forward

### build and run containers

```
git clone https://github.com/somnonetz/xnat-docker-compose
cd xnat-docker-compose
docker-compose build
docker-compose up -d
```

### set up xnat

* go to `http://localhost/xnat` and login
* create a project and subject matching the values in your `copla-editor` config (by default they are `project1` and `subject1`)
* go to `Administer -> Data Types -> Set Up Additional Data types` and add all the `snet01:*` data-types
* go to `Administer -> Site Administration -> Security` and disable CSRF tokens

### finished!

* you should now be able to access `xnat` at `http://localhost/xnat` and `copla-editor` at `http://localhost/copla-editor/`

## Developing

If you want to use this as a development environment for `copla-editor` and `snet-plugin`, set up is a bit more involved

### get the code

* grab `xnat-docker-compose`, `copla-editor` and `snet-plugin`

```
git clone https://github.com/somnonetz/xnat-docker-compose
git clone https://github.com/somnonetz/copla-editor
git clone https://github.com/somnonetz/snet-plugin
```

### set up local volumes mounts

* `cd  xnat-docker-compose/`
* edit `docker-compose.yml` and uncomment the following lines:

```
      # - ./xnat/plugins:/data/xnat/home/plugins
```

and

```
    # volumes:
    #   - ../copla-editor/sn-editor/build:/var/www/copla-editor
```

### set up copla-editor

* `cd copla-editor/sn-editor/`
* create the file `src/config.js` and add the following:

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
* `cd ../..`

### build snet-plugin

```
cd snet-plugin/
./gradlew jar
cp build/libs/snet01-plugin-1.0.0.jar ../xnat-docker-compose/xnat/plugins
cd ..
```

## Troubleshooting

* You can find xnat's logs on the host at: `./xnat-data/home/logs`
* To get a shell on a running `xnat-web` container and access the file system run: `docker-compose exec xnat-web bash`
