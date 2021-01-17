## General structure
Yaml files are used to deploy different objects inside Kubernetes. With these files it is easy to document the deployed services and pods and change things for the deployment.
All files can be interpreted by kubectl by converting them into JSON files. This is done automatically by Kubernetes and is not present to the user. Based on the current structure of the cluster the applied files change an existing object or create new ones.
The base structure of any yaml file for Kubernetes is as following:
````yaml
apiversion:
kind:
metadata:
spec:
````