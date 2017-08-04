# Development
## Build
### Docs
* https://github.com/kubernetes/kubernetes/tree/master/build/
* https://kubernetes.io/docs/getting-started-guides/binary_release/

### Install Docker
Which version is good? [kubeadmin installation guide](https://kubernetes.io/docs/setup/independent/install-kubeadm/#installing-kubectl) says:
```
Version 1.12 is recommended, but v1.10 and v1.11 are known to work as well. Versions 1.13 and 17.03+ have not yet been tested and verified by the Kubernetes node team.
```

Older versions of docker (docker-engine version 1.11.2) can be found in Google's older instructions of kubeadm (it is now removed).
```
apt-get update && apt-get install -y apt-transport-https
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
deb http://apt.kubernetes.io/ kubernetes-xenial main
EOF
apt-get update
apt-get install -y docker-engine

# docker without sudo
sudo gpasswd -a $USER docker
newgrp docker
```

### Build
```
git clone https://github.com/kubernetes/kubernetes.git
cd kubernetes
build/run.sh make # Build just linux binaries in the container. Pass options and packages as necessary.

# Copy built binaries from docker to local disk
# Add "set -x" to build/copy-output.sh can show the details of the copy process:
#  * create a docker container from the build images, and run rsyncd.sh, e.g.
#     docker run --name=kube-rsync-2efd32e9e4-5-v1.8.3-2 --user=1000:999 --hostname=ming --volumes-from kube-build-data-2efd32e9e4-5-v1.8.3-2 -p 127.0.0.1::8730 -d --env KUBE_FASTBUILD=false --env KUBE_BUILDER_OS=linux-gnu --env KUBE_VERBOSE=5 --env GOFLAGS= --env GOLDFLAGS= --env GOGCFLAGS= --interactive --tty kube-build:build-2efd32e9e4-5-v1.8.3-2 /rsyncd.sh
#  * run rsync, e.g.
#     rsync --archive --prune-empty-dirs --password-file=/home/mingzhao/kubernetes/_output/images/kube-build:build-2efd32e9e4-5-v1.8.3-2/rsyncd.password '--filter=- /_temp/' '--filter=+ /vendor/' '--filter=+ /Godeps/' '--filter=+ /staging/***/Godeps/**' '--filter=+ /_output/dockerized/bin/**' '--filter=+ zz_generated.*' '--filter=+ generated.proto' '--filter=+ *.pb.go' '--filter=+ types.go' '--filter=+ */' '--filter=- /**' rsync://k8s@127.0.0.1:32770/k8s/ /home/mingzhao/kubernetes
build/copy-output.sh

```

## Pull Request
https://github.com/kubernetes/community/blob/master/contributors/devel/pull-requests.md


# Scalability - Building Large Scale Clusters
https://kubernetes.io/docs/admin/cluster-large/ at v1.7
* No more than 2k nodes
* No more than 60k pods.

# Networking

https://github.com/projectcalico/canal

Canal is a community-driven initiative that aims to allow users to easily deploy Calico and flannel networking together as a unified networking solution - combining Calico’s industry-leading network policy enforcement with the rich superset of Calico and flannel overlay and non-overlay network connectivity options.

# Alternative Runtimes
https://www.infoq.com/news/2017/04/alternative-kubernetes-runtimes
