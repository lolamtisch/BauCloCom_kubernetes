## Build

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