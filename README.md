# snet-asclepios-docker-compose

Encrypted sleep research on [docker](https://www.docker.com/). Includes [XNAT](https://xnat.org/) with [snet-asclepios-plugin](https://github.com/somnonetz/snet-asclepios-plugin), [copla-editor](https://github.com/somnonetz/copla-editor) and [snet-asclepios-search](https://github.com/somnonetz/snet-asclepios-search)

### Run

* First you need run the ASCLEPIOS services

```command
git clone https://github.com/somnonetz/asclepios-docker-compose.git
cd asclepios-docker-compose
docker-compose up -d
cd ..
```

* Then you can set up and run the snet services 

```command
git clone https://github.com/somnonetz/snet-asclepios-docker-compose.git
cp .env.example .env # edit this file to configure
mkdir -p data/nginx/conf.d
cp snet.local.conf data/nginx/conf.d

mkdir -p data/xnat
cp openid-provider.properties data/xnat/

docker-compose up -d
```

### Set up Keycloak

* TODO

### Set up xnat

#### Site settings

* Go to `http://xnat.localhost/xnat` and login
* Go to `Administer -> Site Administration -> Security` and disable CSRF tokens
* Go to `Administer -> Data Types -> Set Up Additional Data types` and set `snet02:encPsgScanData*` as unsecure
* Go to `Administer -> Site Administration -> Pipeline Settings` and set the processing url to `http://xnat:8080/xnat`
* Create at least one project and subject

### Finished!

You should now be able to access the following applications:

* `xnat` at `http://xnat.localhost/xnat`
* `copla-editor` at `http://xnat.localhost/sn-editor`
* `snet-asclepios-search` at `http://xnat.localhost/asclepios-search`

## Troubleshooting

* You can find xnat's logs on the host at: `./data/xnat/home/logs`
* To get a shell on a running `xnat` container and access the file system run: `docker-compose exec xnat bash`
