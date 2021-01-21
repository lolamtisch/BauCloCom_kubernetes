## Build

### Set Storage
```
kubectl apply -f ./kubernetes/storage.yaml
```

### Set nextcloud namespace
```
kubectl create ns nextcloud
```

### Load secrets
Set the secrets in `./kubernetes/secret.yaml` accordingly. Keep in mind the values have to be base64 encoded and load it into your cluster
```
kubectl apply -f ./kubernetes/secret.yaml
```
### Install kustomize
Get the correct Binaries for your System and place it in the root of the project
```
https://kubectl.docs.kubernetes.io/installation/kustomize/binaries/
```
### Install Krew & MinIO
Install krew (git is required)
```
(
  set -x; cd "$(mktemp -d)" &&
  curl -fsSLO "https://github.com/kubernetes-sigs/krew/releases/latest/download/krew.tar.gz" &&
  tar zxvf krew.tar.gz &&
  KREW=./krew-"$(uname | tr '[:upper:]' '[:lower:]')_$(uname -m | sed -e 's/x86_64/amd64/' -e 's/arm.*$/arm/')" &&
  "$KREW" install krew
)
```
Set krew namespace
```
export PATH="${KREW_ROOT:-$HOME/.krew}/bin:$PATH"
```
Update krew and install minIO
```
 kubectl krew update
 kubectl krew install minio
 kubectl minio init
```

### Build cluster yaml
```
./kustomize build -o ./dist/cluster-config.yaml ./kubernetes/
```
### Apply to the cluster
```
kubectl apply -f ./dist/cluster-config.yaml
```