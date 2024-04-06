# Central Authentication Service (CAS) [![License](https://img.shields.io/hexpm/l/plug.svg)](https://github.com/Jasig/cas/blob/master/LICENSE)

## Introduction

This repository hosts the [Docker](https://www.docker.com/) build configuration necessary to build
a [CAS](https://github.com/apereo/cas) image.
See the `Dockerfile` for more info.

## Versions

A docker image for CAS server. Images are tagged to match CAS server releases.

## Requirements

* Docker version `1.9.x` ~ `1.13.x`

## Getting started

### Build CAS

1. Clone cas-webapp-docker repo https://github.com/jgregorio0/cas-webapp-docker/tree/5.3
2. Create a new certificate into keystore

``` 
keytool -genkey -keyalg RSA -alias cas -keystore thekeystore -storepass changeit -validity 360 -keysize 2048 -dname "${DNAME:-CN=cas.example.org,OU=Example,OU=Org,C=US}" -ext SAN="${CERT_SUBJ_ALT_NAMES:-dns:example.org,dns:localhost,ip:127.0.0.1}"
```

3. Default configuration applied into CAS properties /etc/cas/config/cas.properties and CAS service
   /etc/cas/services/Everything-1.json
4. Build & run

```
sudo ./build.sh 5.3
sudo ./run.sh 5.3
```
- shared folder is created under ~/shared:/shared directory

5. Access CAS

- URL: https://localhost:8443/cas/login
- user: casuser
- pass: Mellon

### Start after shutdown

6. Once have been shutdown, start CAS container `sudo docker container start cas`
7. Check logs `sudo docker logs --follow cas`
8. Access CAS

- URL: https://localhost:8443/cas/login
- user: casuser
- pass: Mellon

### CAS container configuration

9. Access CAS terminal `sudo docker exec -it cas sh`
10. Build CAS cli `/cas-overlay/build.sh cli`
11. Run cli `java -jar /cas-overlay/target/cas-server-support-shell-5.3.16.jar -sh`
11. Set CAS configuration file `vi /cas-overlay/target/cas/WEB-INF/classes/application.properties`
 

## Configuration

### Image

* The image will be available on the host via ports `8080` and `8443`
* You must check the `Dockerfile` to ensure the right branch from
  the [CAS overlay project](https://github.com/apereo/cas-overlay-template) is pulled/cloned.
* Check the [CAS overlay project](https://github.com/apereo/cas-overlay-template) itself to figure out the targetted CAS
  release.

### SSL

* Update the `thekeystore` file with the server certificate and chain if you need access the CAS server via HTTPS.
* The password for the keystore is `changeit`.
* The build will automatically copy the keystore file to the image. The embedded container packaged in the overlay is
  pre-configured to use that keystore for HTTPS requests.
* The build will also auto-copy configuration files under the `etc/cas` directory to the corresponding locations inside
  the image.

## Build [![](https://badge.imagelayers.io/apereo/cas:latest.svg)](https://imagelayers.io/?images=apereo/cas:latest 'apereo cas')

**Make sure** that both `build.sh` and `run.sh` are updated to build the appropriate tag. Docker tags **MUST**
correspond
to CAS server versions. Also, make sure the version matches a branch name on
the [CAS overlay project](https://github.com/apereo/cas-overlay-template/branches).

**NOTE:** On windows, you may want to run `bash` first so you can execute shell scripts.

```bash
./build.sh $CasVersion
```

The image will be built as `apereo/cas:v$CasVersion`.

## Run

```bash
./run.sh $CasVersion
```

## Release

* New images shall be released at the time of a new CAS server release.
* Image versions are reflected in the `build|run.sh` files and need to be updated per CAS/Image release.
* Images are published to [https://hub.docker.com/r/apereo/cas/](https://hub.docker.com/r/apereo/cas/)

```bash
./push.sh $CasVersion
```


