## mymediawiki
This repository contains files for setting up Mediawiki v1.36 on kubernetes cluster. Mediawiki is setup using Helm package manager for the purpose of automated deployment.
Components of Mediawiki helm chart:
1. Mediawiki deployment files
2. Mysql DB deployment files

### Creating Docker image

1. Docker image for Mediawiki:
- mediawiki_v1.36/Dockerfile - For installing and configuring Mediawiki v1.36 on Ubuntu base OS.

Use below steps to create docker image:- 
- cd mediawiki_v1.36
- sudo docker build -f Dockerfile -t mediawiki:v1 .

2. Docker image for Mysql DB
- mysql:latest (pulled from dockerhub) 

### Deploying the stack on kubernetes

* The helm chart consists of below components:
  - **mymediawiki/templates/** : Contains files to create k8s objects for DB and app deployment: my-mediawiki-db-deployment.yml, my-mediawiki-db-svc.yml, my-mediawiki-db-pv-pvc.yml, my-mediawiki-db-configmap.yml, my-mediawiki-db-secret.yml, my-mediawiki-deployment.yml, my-mediawiki-svc.yml, my-mediawiki-hpa.yml
  - **mymediawiki/values.yaml** : File which consists of pre-configured values that are used for deployment of the stack on k8s. The same can be modified for further generalisation/usage.

1. On kubernetes cluster, execute below commands to bring up Mediawiki application in mediawiki namespace:
- kubectl create namespace mediawiki
- helm install mymediawiki ./mymediawiki --namespace mediawiki

2. Once both the Mediawiki and DB pods are up and running, the app UI can be accessed using the Mediawiki service externalIP and port (as specified in values.yaml file - can be changed for deploying the stack based on user's k8s env)
e.g http://<ip>:<port>

3. Configure the Mediawiki by providing the values as below:

- Database Host : my-mediawiki-db
- Database Name : wikidatabase
- Database Username : wikiuser
- Database Password : Passw0rd

4. After the DB connections are setup, provide details for setting up your new Wiki (wikiname, username and create new password).

5. After final submission, a file namely LocalSettings.php is generated which needs to be copied on the mediawiki container at location: /var/lib/mediawiki.
For further use, create new docker image by adding command to copy the downloaded php file in Mediawiki container (in Dockerfile). Update the values.yaml file and correspondingly upgrade the helm chart to reflect new changes.

6. Once the new deployment is up, access mediawiki at below URL:
http://<ip>:<port>/mediawiki  (as mentioned in point 2, IP and port can be changed in values.yaml file in helm chart)

7. On UI page that comes up on above URL, try logging in via the credenatials you provided earlier (as in step 4). Login is successful.

### _Important points to consider_

1. The values for Username(MYSQL_USER) and Password(MYSQL_PASSWORD) for DB access are externalised via K8s Secrets - placed in my-mediawiki-db-secret.yml file and are encrypted using base64 encoding.
2. The database name is also externalised using K8s ConfigMap & is mentioned in my-mediawiki-db-configmap.yml file.
3. The database has been persisted using PersistentVolume object of k8s using node hostpath & the same would be claimed using PersistentVolumeClaim as in file my-mediawiki-db-pv-pvc.yml
4. Init container is added in the mediawiki app deployment file, which tests startup of MySql DB and allows mediawiki start only after its database is up and running.
5. Horizontal Pod Autoscaler(HPA) is created for the application as in file my-mediawiki-hpa.yml which takes care of autoscaling feature for the mediawiki application.
