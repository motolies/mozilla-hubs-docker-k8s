# Clone all componenets

``` bash
# clone all repos
git clone https://gitlab.nautilus.optiputer.net/ar-noc/reticulum.git
git clone https://gitlab.nautilus.optiputer.net/ar-noc/hubs.git
git clone https://gitlab.nautilus.optiputer.net/ar-noc/Spoke.git
git clone https://gitlab.nautilus.optiputer.net/ar-noc/mozilla-hubs-docker.git

# switch to k8s branch for recent dockerfiles
pushd reticulum
git checkout k8s
popd
pushd hubs
git checkout k8s
popd
pushd Spoke
git checkout k8s
popd

```

# Build docker images

Assuming you have docker installed..

```
cd mozilla-hubs-docker
docker-compose build
docker-compose up
```

then browse to:

- hubs: http://localhost:8080/
- Spoke: http://localhost:8081/

some assets and images will not be visible yet