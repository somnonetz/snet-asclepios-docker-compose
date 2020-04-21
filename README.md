# snet-docker-compose

Sleep research apps on [docker](https://www.docker.com/). Includes [XNAT](https://xnat.org/) with [snet-plugin](https://github.com/somnonetz/snet-plugin), [copla-editor](https://github.com/somnonetz/copla-editor) and [snet-asclepios-search](https://github.com/somnonetz/snet-asclepios-search)


## Usage

If you just want to run xnat in docker with the latest stable versions of `snet-plugin` and `copla-edtior`, set up is fairly straight forward

### Run

```command
cp .env.example .env # edit this file to configure
mkdir -p data/nginx/conf.d
cp xnat.local.conf data/nginx/conf.d

docker network create asclepios
docker-compose up -d
```

### Set up xnat

* Go to `http://localhost/xnat` and login
* Go to `Administer -> Site Administration -> Security` and disable CSRF tokens
* Go to `Administer -> Data Types -> Set Up Additional Data types` and set `snet01:psgScanData*` as unsecure
* Go to `Administer -> Site Administration -> Pipeline Settings` and set the processing url to `http://xnat:8080/xnat`
* Create at least one project and subject

### Finished!

* You should now be able to access `xnat` at `http://localhost/xnat` and `copla-editor` at `http://localhost/copla-editor/`

## Troubleshooting

* You can find xnat's logs on the host at: `./xnat-data/home/logs`
* To get a shell on a running `xnat` container and access the file system run: `docker-compose exec xnat bash`