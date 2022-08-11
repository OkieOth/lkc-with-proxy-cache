# Requirements
* docker
* kubectl (https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/#install-using-native-package-management)
* krew (https://krew.sigs.k8s.io/docs/user-guide/setup/install/)
* kubectx (https://github.com/ahmetb/kubectx#kubectl-plugins-macos-and-linux)
* fzf (https://github.com/junegunn/fzf)

# External documentations
* https://github.com/rpardini/docker-registry-proxy/pkgs/container/docker-registry-proxy
* https://kind.sigs.k8s.io/



# How to install
```bash
# download the official registry image
docker pull ghcr.io/rpardini/docker-registry-proxy:0.6.4

# run the registry locally 
docker run -u $(id -u ${USER}):$(id -g ${USER}) \
    -v `pwd`/registry_conf.yml:/etc/docker/registry/config.yml \
    -v `pwd`/registry_storage:/storage \
    --rm -d \
    -p 5000:5000 \
    --name registry registry:2
```

# How to bring up the kind cluster
## Offline working
By default kind use a *latest* image for its nodes. To enable offline working
you have manually to download an image with defined tag and use this then to
create the cluster.
```bash
# download a well tagged image for offline working
# (https://hub.docker.com/r/kindest/node/tags)
docker pull kindest/node:v1.24.3

# create a cluster
kind create cluster --name eiko_01

# create cluster in a offline case (with pre downloaded image)
kind create cluster --name eiko-01 \
     --image kindest/node:v1.24.3 \
     --config=example_config.yaml

# destroy a cluster
kind delete cluster --name eiko_01
```

# Trouble Shooting
If a cluster fails while creating, restart with the `--remain` option.
That allows you to investigate the logs later.

When the docker logs mention failures as reason of too many open files (e.g. when
starting two multi-node clusters). Do the following:

```bash
# source: https://cluster-api.sigs.k8s.io/user/troubleshooting.html
sysctl fs.inotify.max_user_watches=1048576
sysctl fs.inotify.max_user_instances=8192
```
