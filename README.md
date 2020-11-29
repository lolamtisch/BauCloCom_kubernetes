## Build

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
### Build cluster yaml
```
./kustomize build -o ./dist/cluster-config.yaml ./kubernetes/
```
### Apply to the cluster
```
kubectl apply -f ./dist/cluster-config.yaml
```