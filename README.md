# micro-frontends-in-kubernetes
micro frontends application works in kubernetes

fork by https://github.com/neuland/micro-frontends

# Usage
## My Env
```
$ sw_vers
ProductName:	Mac OS X
ProductVersion:	10.14.6
BuildVersion:	18G84
$ docker -v
Docker version 19.03.1, build 74b1e89
```

## Install
use [kind](https://github.com/kubernetes-sigs/kind)

prepare kind.
```bash
$ brew install go
$ go version
go version go1.13 darwin/amd64
$ PATH=${PATH}:$(go env GOPATH)/bin
$ kind create cluster
Creating cluster "kind" ...
 ✓ Ensuring node image (kindest/node:v1.15.3) 🖼
 ✓ Preparing nodes 📦
 ✓ Creating kubeadm config 📜
 ✓ Starting control-plane 🕹️
 ✓ Installing CNI 🔌
 ✓ Installing StorageClass 💾
Cluster creation complete. You can now use the cluster with:

export KUBECONFIG="$(kind get kubeconfig-path --name="kind")"
kubectl cluster-info
$ export KUBECONFIG="$(kind get kubeconfig-path --name="kind")"
```

## Build & Load
```bash
$ cd team-blue && docker build -t team-blue:1.0.0 . && cd ..
$ cd team-green && docker build -t team-green:1.0.0 . && cd ..
$ cd team-red && docker build -t team-red:1.0.0 . && cd ..
$ cd team-nginx && docker build -t team-nginx:1.0.0 . && cd ..
```

※ The yaml files was generated using [kompose](https://github.com/kubernetes/kompose)

```bash
$ kind load docker-image team-blue:1.0.0
$ kind load docker-image team-green:1.0.0
$ kind load docker-image team-red:1.0.0
$ kind load docker-image team-nginx:1.0.0
```

## Apply
```bash
$ find . -name "*.yaml"|xargs -I {} kubectl apply -f {}
$ nginx_pod=$(k get pods | grep nginx | awk '{print $1}')
$ kubectl port-forward $nginx_pod 3000:3000
```

## Delete
```bash
$ find . -name "*.yaml"|xargs -I {} kubectl delete -f {}
$ kind delete cluster --name kind
```

```bash
$ team_blue_image=$(docker images | grep team-blue | awk '{print $3}')
$ team_green_image=$(docker images | grep team-green | awk '{print $3}')
$ team_red_image=$(docker images | grep team-red | awk '{print $3}')
$ team_nginx_image=$(docker images | grep team-nginx | awk '{print $3}')
$ docker rmi $team_blue_image $team_green_image $team_red_image $team_nginx_image
```
