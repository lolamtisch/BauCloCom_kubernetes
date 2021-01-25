## General structure
Yaml files are used to deploy different objects inside Kubernetes. With these files it is easy to document the deployed services and pods and change things for the deployment.
All files can be interpreted by kubectl by converting them into JSON files. This is done automatically by Kubernetes and is not present to the user. Based on the current structure of the cluster the applied files change an existing object or create new ones. <br>
The base structure of any yaml file for Kubernetes is as following:
````yaml
apiversion:
kind:
metadata:
spec:
````
The `apiversion` specifies the version of Kubernetes API which is used to create the object. <br>
The `kind` describes what kind of object you want to create. Examples are `Deployment` for the creation of pods or `Service` *hier wäre noch ein Beispiel oder eine Erklärung für Service gut*. <br>
The `metadata` is describing data which helps to uniquely identify the object. It often contains of a name, a unique identificator as well as a namespace. <br>
The `spec`is then used to specify the kind of object you want to create. This is different for each kind and has different sub categories based on this. For example a database deployment needs a user and a passwort as well as a service to connect to. A service on the other side only needs a port.

To create a object based on a file in kubectl you have to use the `kubectl apply` command. It is used as following: 
````
kubectl apply -f path/to/yaml/file.yaml
````
The option `-f` indicates, that we are applying a file. <br>
In the following all important aspects of the clusters are described based on the yaml files which have created the cluster in earlier stages.

## Namespace.yaml
The namespace.yaml is the first file which is applied. It creates a new object of kind `Namespace` with the name 'Nextcloud' inside the cluster. With this the other objects can be created inside the namespace to distinguish between different applications, if deployed in the same cluster. <br>
Special about this yaml file is the fact, that a `spec`option is not needed, because the `metadata: kind:`option is used to specify the name of the namespace.

## Secret.yaml
The secret.yaml is the next file which is applied. It contains all passwords and users for nextcloud as well as mysql. All values inside this file are base64 encoded to not be stolen easily. 
This secrets should be changed for your server every time you build up a new cluster to not allow attackers to get access to your infrastructure easily. <br>
The kind `Secret`is also *slightly* different than the general structure. A `type` of the secret is descibed as well as different variables inside the `data` option.

## Minio
After setting up the namespace and the secret minio is set up to Store the data of nextcloud. For the deployment secrets and tenants are needed. The secrets describe the minio credentials as well as the console keys. These are then used inside the tenants. 
Tenants are a special kind of object which is not present in default kubernetes. To create such an object you have to install minio on the cluster first to use it. The tenant then can be created with the yaml file. Inside the file the amount of replicas is set to two. 
Based on this two console objects are created for minio. Kubernetes automatically detects the amount of servers available in the cluster as well as their workload and then evenly distributes all replicas across the cluster. 
Inside the tenant a persistent volume claim is created to store data not only for the runtime but independent of the pod.