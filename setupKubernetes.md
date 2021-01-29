## Setup Kubernetes cluster
### Introduction
This setup is intended for ubuntu Vms running on VmWare Workstation 16, a different VT-tool can be used,
but it needs to be ensured, that the vms get static IPs.

### Prepare the system
Open the Terminal with ctrl+T. Set terminal to super user
```
sudo su
```
Update system
```
apt-get update
```
Disable swap file (necessary for starting a kubernetes cluster)
```
swapoff -a
```
Comment out the entry for the swapfile with '#' in /etc/fstab
```
nano /etc/fstab
```

Set the hostname according to its role (kmaster of master and kworker0x for each worker). Nodes cant have the same hostname.
```
nano /etc/hostname
```

### Install the necessary plugins
Install OpenSSH-Server if a remote connection is necessary.
```
apt-get install -y openssh-server
```
Install Docker.IO
```
apt-get install -y docker.io
```
Install curl
```
apt-get install -y apt-transport-https curl
```
Install Kubernetes
```
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
```
```
cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
deb http://apt.kubernetes.io/ kubernetes-xenial main
EOF
```
Install Kubeadm, Kubelet, Kubectl
```
apt-get install -y kubelet kubeadm kubectl
```
Open 10-kubeadm.conf and add following line after the last environment variable
```
nano /etc/systemd/system/kubelet.service.d/10-kubeadm.conf

Environment=”cgroup-driver=systemd/cgroup-driver=cgroupfs”
```

Run another update for all plugins to be upadted, afterwards reboot the system (necessary!)
```
apt-get update
```
### For the master only
Set terminal to super user again
```
sudo su
```
Get IP-address of master (if necessary install net-tools)
```
ifconfig
```

Setup of Kubernetes Network. (curly braces must be removed!)
```
kubeadm init --apiserver-advertise-address={ip-address-of-kmaster-vm} --pod-network-cidr=192.168.0.0/16
```
After installation a join command will printed, make a copy for joining workers later on
example:
```
kubeadm join masterIP:6443 --token m33ryp.6rn9refif \
    --discovery-token-ca-cert-hash sha256:d9b187312464d1375d699b0e444
```
Export config (root user is necessary)
```
export KUBECONFIG=/etc/kubernetes/admin.conf
```
Install calico
```
kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml
```
The following command can be used to check if the cluster is running properly
```
kubectl get pods -o wide --all-namespaces
```
Install kubernetes dashboard (must be done before nodes are added, or else its not running on the master)
```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.5/aio/deploy/recommended.yaml
```
Create a service account
```
kubectl -n default create serviceaccount dashboard
```
Create an admin-role
```
kubectl -n default create clusterrolebinding dashboard-admin --clusterrole=cluster-admin --serviceaccount=default:dashboard
```
Setup cluster role binding.
```
kubectl create clusterrolebinding cluster-system-anonymous --clusterrole=cluster-admin --user=system:anonymous
```
Get a token. The output must be stored for later usage.
```
kubectl get secret $(kubectl get serviceaccount dashboard -o jsonpath="{.secrets[0].name}") -o jsonpath="{.data.token}"|base64 -d;echo
```
Start the proxy service
```
kubectl proxy
```
The following link can be opened in the browser to access the cluster dashboard. It can only be reached on the master node and not remotely.
```
http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/
```

### On the workers only
Use the join command that was printed during the setup of the Kubernetes cluster.
```
kubeadm join masterIP:6443 --token xyzExample \
    --discovery-token-ca-cert-hash sha256:xyzExampleHash
```
Afterwards the cluster should contain the worker node. It can be identified by the different hostname.
```
kubectl get pods -o wide --all-namespaces
```