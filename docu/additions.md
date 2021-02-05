# Database
The next step is to setup the database for nextcloud. The database is needed to store administrative data. As database you can setup MySQL, MariaDB, PostgreSQL and a Oracle database, recommended by Nextcloud are the first two options. We tried three different setups, one with MySQL replication, one with MariaDB and one with Maxscale. In the following the yaml files of 3 setups are described.

## MySQL
The MySQL setup includes a statefulset of the database, which automatically forward the included data to all nodes. The setup is done with a persistent volume claim, a configmap, a service as well as the statefulset.

First of all the persistent volume claim is setup to store the data for the database. After this the configmap is created. It is used to configure the master and slave nodes in the cluster. The master is allowed to read and write, the slaves are only allowed to read. After this the service is created which is then used in the statefulset deployment.

The statefulset deployment is a special kind of deployment. In a statefulset the pods are initialized one after the other. So first the master is created and afterwards the childs are created. To clone the data and to set the config different bash commands are used. First the initialization of the statefulset takes place. The server id is generated and the appropriate config file from the configmap are copied to the server. After the generation of the id and the copying of the correct configmap the data is copied from the previous node. So the first child copies the data from the master, the second child from the first child and so on. After this the container for the MySQL image is set up. It contains information like the database name, the password, the user and the port to connect to. It also includes a liveness probe and a readiness probe, which determines the configuration for those two probes. Additionally an extra backup for the data is created.

Unfortunately for this approach to work read and write has to be splitted on application level, which Nextcloud does not support. This means it can only be used as realtime database backup and not for loadbalacing.

## MariaDB
The MariaDB setup has one database for all node, so all nodes connect to this database. For this setup a persistent volume claim, a service as well as the deployment are created.

Like the MySQL setup, first of all the persistent volume claim is created. Also the next step is to create the service, which has no special things inside.

The last step in the setup is to create the MariaDB deployment. The replicas are set to one and the strategy is set to recreate. This means on deployment the old version is terminated and replaced by the new deployment. Besides the information about the database name, the passwort as well as the user the setup also needs some additional arguments. The additional arguments set are the transaction isolation, the binlog format as well as the maximum amount of connections. The transaction isolation is set to READ-COMMITTED, which means, that for every read, its own fresh snapshot is set and read. The binlog format is set to ROW, so for every update, delete or create a new log is created. Additionally DML statements are not logged. The maximum amount of connections is set to 1000, to not use too many connections simultaneously.

## MaxScale
MaxScale is a database proxy for Mariadb replications. The difference between MYSQL replication and MaxScale is that it can automatically route write operations independently from Nextcloud.

The folder mariadb_maxscale contains the configuration to setup Mariadb for Maxscale. It is very similar to the normal mariadb setup with the difference that it is a statefullset and starting the db in replication mode. Maxscale is configured in a configMap which is loaded inside the deployment. As we have two servers the Maxscale replication is set to two. This is the only setup that can be used as a load balancer and failover for the database. Unfortunately the latency performance of this approach is very poor resulting in the Nextcloud UI responding unreasonably slow.

As the database replication solutions we teste did not work well with Nextcloud we decided to use the simple mariadb approach as the recommended and preset approach.

# Continuous Delivery
Continuous Delivery (CD) makes it possible to deploy changes automatically to the server. For this we use GitHub Actions as the pipeline. The configuration can be found in the .github folder and are automatically trigger when pushing a commit to the master branch.

## Image Building
The first task of the deployment is to build a Nextcloud image and save it into the Github Container Registry using the dockerfile in the folder `dockerfiles/nextcloud`. For Kubernetes docker images have to be build and hosted outside of the cluster in registries. For the deployment to work the sectete `CR_PAT` has to be set in the secrets section of the GitHub repository. It should contain a `Personal Access Token` with the permission `repo` and `write:packages`, which can be create in the GitHub User settings in the developer section.

## Deploying
The Second task is to deploy the yaml configuration into the Cluster on the server. We only deploy the files that are build by Kustomize, as they contain no cluster specific configuration like secrets or volume size configurations. The kubeconfig is needed for the upload to the Server. Which has to be set as a string inside of the  `KUBE_CONFIG_DATA` secret in the GitHub repository.
