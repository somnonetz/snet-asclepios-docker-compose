## Developing

This can be used as a development environment for sleep research applications. For example bellow are instructions to get set up for developing `copla-editor` and `snet-plugin`.

### Get the code

* Grab `snet-docker-compose`, `copla-editor`, `snet-plugin`

```command
git clone https://github.com/somnonetz/snet-docker-compose
git clone https://github.com/somnonetz/copla-editor
git clone https://github.com/somnonetz/snet-plugin
```

### Set up local volumes mounts

* `cd  snet-docker-compose/`
* Edit `docker-compose.yml` and uncomment the following lines:

```yml
      # - ./data/xnat/plugins:/data/xnat/home/plugins
```

and

```yml
    # volumes:
    #   - ../copla-editor/sn-editor/build:/var/www/copla-editor
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

### Run

```command
cp .env.example .env # edit this file to configure
mkdir -p data/nginx/conf.d
cp xnat.local.conf data/nginx/conf.d

docker network create asclepios
docker-compose up -d
```

### Finished!

Now you can replace the `copla-editor` and `snet-plugin` builds at run time and test your changes.