# Introduction to Kubernetes

Kubernetes is a portable and scalable open-source-platform to manage container-based applications and services. It eases up the configuration as well as the automatization. Joe Beda, Brendan Burns and Craig McLuckie founded Kubernetes just before more Google employees collaborated. (Wired, 2015) 2014 Kubernetes has been announced. (Metz, 2014) Version 1.0 has been published on the 21st July 2015. On that day the project also was founded to the Cloud Native Computing Foundation and remained then open source. (pro-linux, 2015)

Kubernetes coordinates computer-, network- and storage infrastructure. It has aspects of the platform as a service (paas) as well as infrastructure as a service (iaas). It also allows the portability between different infrastructure providers.

The main components of Kubernetes are the master and the node components. The master component is the control area of the cluster. On the one hand it makes decisions for the cluster such as time scheduling. On the other hand, it identifies and react to actions in the cluster, for example the shut down of one node component. On node components the work for the application or the service is done.

The system orchestrates so called pods as smallest deployable unit on the nodes. Each pod contains one or several containers. These containers share the storage and network resources of the master node. Each container has its own specification on how to run the container. To access the pod, they have an unique IP-address. However, each time a pod fails a new one has to be created and a new IP address is created as well.

For different pods to communicate as well as to communicate with the user’s services are created. There are several services which can be created for example a network service for the user to access the application. The Services manage different port forwarding and support the pods to get the relevant data from other pods. Another example for a service is a load balancer. The load balancer analyses the volume of workload on different nodes and automatically distributes the workload evenly. 


Von Khtan66 - Eigenes Werk, CC BY-SA 4.0, https://commons.wikimedia.org/w/index.php?curid=53571935


https://www.wired.com/2015/06/google-kubernetes-says-future-cloud-computing/
Cade Metz: Google Open Sources Its Secret Weapon in Cloud Computing. In: Wired. 10. Juni 2014, ISSN 1059-1028
Cloud Native Computing Foundation soll Container-Technologien zusammenbringen. In: pro-linux.de. 22. Juni 2015.
https://kubernetes.io/de/docs/concepts/overview/what-is-kubernetes/

