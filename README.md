# UdaConnect
## Overview
### Background
Conferences and conventions are hotspots for making connections. Professionals in attendance often share the same interests and can make valuable business and personal connections with one another. At the same time, these events draw a large crowd and it's often hard to make these connections in the midst of all of these events' excitement and energy. To help attendees make connections, we are building the infrastructure for a service that can inform attendees if they have attended the same booths and presentations at an event.

### Goal
You work for a company that is building a app that uses location data from mobile devices. Your company has built a [POC](https://en.wikipedia.org/wiki/Proof_of_concept) application to ingest location data named UdaTracker. This POC was built with the core functionality of ingesting location and identifying individuals who have shared a close geographic proximity.

Management loved the POC so now that there is buy-in, we want to enhance this application. You have been tasked to enhance the POC application into a [MVP](https://en.wikipedia.org/wiki/Minimum_viable_product) to handle the large volume of location data that will be ingested.

To do so, ***you will refactor this application into a microservice architecture using message passing techniques that you have learned in this course***. It’s easy to get lost in the countless optimizations and changes that can be made: your priority should be to approach the task as an architect and refactor the application into microservices. File organization, code linting -- these are important but don’t affect the core functionality and can possibly be tagged as TODO’s for now!

### Technologies
* [Flask](https://flask.palletsprojects.com/en/1.1.x/) - API webserver
* [SQLAlchemy](https://www.sqlalchemy.org/) - Database ORM
* [PostgreSQL](https://www.postgresql.org/) - Relational database
* [PostGIS](https://postgis.net/) - Spatial plug-in for PostgreSQL enabling geographic queries]
* [Vagrant](https://www.vagrantup.com/) - Tool for managing virtual deployed environments
* [VirtualBox](https://www.virtualbox.org/) - Hypervisor allowing you to run multiple operating systems
* [K3s](https://k3s.io/) - Lightweight distribution of K8s to easily develop against a local cluster

## Running the app
The project has been set up such that you should be able to have the project up and running with Kubernetes.

### Prerequisites
We will be installing the tools that we'll need to use for getting our environment set up properly.
1. [Install Docker](https://docs.docker.com/get-docker/)
2. [Set up a DockerHub account](https://hub.docker.com/)
3. [Set up `kubectl`](https://rancher.com/docs/rancher/v2.x/en/cluster-admin/cluster-access/kubectl/)
4. [Install VirtualBox](https://www.virtualbox.org/wiki/Downloads) with at least version 6.0
5. [Install Vagrant](https://www.vagrantup.com/docs/installation) with at least version 2.0

### Environment Setup
To run the application, you will need a K8s cluster running locally and to interface with it via `kubectl`. We will be using Vagrant with VirtualBox to run K3s.

#### Initialize K3s
In this project's root, run `vagrant up`. 
```bash
$ vagrant up
```
The command will take a while and will leverage VirtualBox to load an [openSUSE](https://www.opensuse.org/) OS and automatically install [K3s](https://k3s.io/). When we are taking a break from development, we can run `vagrant suspend` to conserve some ouf our system's resources and `vagrant resume` when we want to bring our resources back up. Some useful vagrant commands can be found in [this cheatsheet](https://gist.github.com/wpscholar/a49594e2e2b918f4d0c4).

#### Set up `kubectl`
After `vagrant up` is done, you will SSH into the Vagrant environment and retrieve the Kubernetes config file used by `kubectl`. We want to copy the contents of this file into our local environment so that `kubectl` knows how to communicate with the K3s cluster.
```bash
$ vagrant ssh
```
You will now be connected inside of the virtual OS. Run `sudo cat /etc/rancher/k3s/k3s.yaml` to print out the contents of the file. You should see output similar to the one that I've shown below. Note that the output below is just for your reference: every configuration is unique and you should _NOT_ copy the output I have below.

Copy the contents from the output issued from your own command into your clipboard -- we will be pasting it somewhere soon!
```bash
$ sudo cat /etc/rancher/k3s/k3s.yaml

apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUJkakNDQVIyZ0F3SUJBZ0lCQURBS0JnZ3Foa2pPUFFRREFqQWpNU0V3SHdZRFZRUUREQmhyTTNNdGMyVnkKZG1WeUxXTmhRREUyTXpNME1EazFPREF3SGhjTk1qRXhNREExTURRMU16QXdXaGNOTXpFeE1EQXpNRFExTXpBdwpXakFqTVNFd0h3WURWUVFEREJock0zTXRjMlZ5ZG1WeUxXTmhRREUyTXpNME1EazFPREF3V1RBVEJnY3Foa2pPClBRSUJCZ2dxaGtqT1BRTUJCd05DQUFSV0gvT0dETExTUDFnU2V4WDBmWUw1VXl4MVZnZnlwS3hHMG1OVGhWaVUKMnA1MVFHdGdwQnlpSkhxWXhHYU5CUkYxM2xiVFhxVXNPMDZwcUJoRms2WDVvMEl3UURBT0JnTlZIUThCQWY4RQpCQU1DQXFRd0R3WURWUjBUQVFIL0JBVXdBd0VCL3pBZEJnTlZIUTRFRmdRVVBCQ1BuMTVMcUdOeHBrK09NUWNtCnkyTDFLVGN3Q2dZSUtvWkl6ajBFQXdJRFJ3QXdSQUlnS3prVUFKMDJYUURjTThuKzhXZDFGUEVaYy9vdVB4ZEcKQlVkdkUxUUcyTllDSURrTW5FTWxKNytIVUpzdW9jSEtMV2NkSXdHeTVMQnpGaitkYjZva01yU0QKLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=
    server: https://127.0.0.1:6443
  name: default
contexts:
- context:
    cluster: default
    user: default
  name: default
current-context: default
kind: Config
preferences: {}
users:
- name: default
  user:
    client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUJrVENDQVRlZ0F3SUJBZ0lJVEVmbUlVNnlxNUF3Q2dZSUtvWkl6ajBFQXdJd0l6RWhNQjhHQTFVRUF3d1kKYXpOekxXTnNhV1Z1ZEMxallVQXhOak16TkRBNU5UZ3dNQjRYRFRJeE1UQXdOVEEwTlRNd01Gb1hEVEl5TVRBdwpOVEEwTlRNd01Gb3dNREVYTUJVR0ExVUVDaE1PYzNsemRHVnRPbTFoYzNSbGNuTXhGVEFUQmdOVkJBTVRESE41CmMzUmxiVHBoWkcxcGJqQlpNQk1HQnlxR1NNNDlBZ0VHQ0NxR1NNNDlBd0VIQTBJQUJFZVd1bTl0SktWa0ZGbjUKMmdmVUtZN1hSY0ZMQTAxNmZrcHBRNWJSclYzQitTTXo0VFNwNnE0TGpJaGdmZVpaTGlHYm1GU0l3RWgvcmRPdQphdGFSbFYralNEQkdNQTRHQTFVZER3RUIvd1FFQXdJRm9EQVRCZ05WSFNVRUREQUtCZ2dyQmdFRkJRY0RBakFmCkJnTlZIU01FR0RBV2dCUkFkZDg1Y003ekpnUzMzSGt3RERsRVkwNW80ekFLQmdncWhrak9QUVFEQWdOSUFEQkYKQWlFQXN0QytBWENmM3Q2YTFBemtYMkJQWXpEQytIRXFSTUVtbDRsRHRTc1JiL0VDSUczUGJoRFhEd3hXcHpCWAo5SS8yTnRPRmxOb1B0MlJxbFRBQ0RxaHpEZE13Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0KLS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUJkekNDQVIyZ0F3SUJBZ0lCQURBS0JnZ3Foa2pPUFFRREFqQWpNU0V3SHdZRFZRUUREQmhyTTNNdFkyeHAKWlc1MExXTmhRREUyTXpNME1EazFPREF3SGhjTk1qRXhNREExTURRMU16QXdXaGNOTXpFeE1EQXpNRFExTXpBdwpXakFqTVNFd0h3WURWUVFEREJock0zTXRZMnhwWlc1MExXTmhRREUyTXpNME1EazFPREF3V1RBVEJnY3Foa2pPClBRSUJCZ2dxaGtqT1BRTUJCd05DQUFRNXZKMW8rUHQ4L1lheGhBaVQwaFU5U1lmMU9scDIwc1REMmxoZERVR2QKcEozZTlGQndIT1hlelViZk4vajZFVG96YkxOV01GdU03QWtYR0NCYjU4em5vMEl3UURBT0JnTlZIUThCQWY4RQpCQU1DQXFRd0R3WURWUjBUQVFIL0JBVXdBd0VCL3pBZEJnTlZIUTRFRmdRVVFIWGZPWERPOHlZRXQ5eDVNQXc1ClJHTk9hT013Q2dZSUtvWkl6ajBFQXdJRFNBQXdSUUlnSm5NcGFzT0E3Sy9sb29ReWZsQTZveW5iak5uSTI4dncKbm11aVVtYUxBRDRDSVFEeWVhclBIL3BuNWxYMUo0dGNnOTR4c1VIMlRIZ0RTcnVoaWxTL1Qxckc5UT09Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K
    client-key-data: LS0tLS1CRUdJTiBFQyBQUklWQVRFIEtFWS0tLS0tCk1IY0NBUUVFSU1Za2srdjhmR0FVckZJdWx0TXZWTEZ4Tm9uMms4RlV2ZS9GT1JnaWRZc25vQW9HQ0NxR1NNNDkKQXdFSG9VUURRZ0FFUjVhNmIyMGtwV1FVV2ZuYUI5UXBqdGRGd1VzRFRYcCtTbWxEbHRHdFhjSDVJelBoTktucQpyZ3VNaUdCOTVsa3VJWnVZVklqQVNIK3QwNjVxMXBHVlh3PT0KLS0tLS1FTkQgRUMgUFJJVkFURSBLRVktLS0tLQo=
```
Type `exit` to exit the virtual OS and you will find yourself back in your computer's session. Create the file (or replace if it already exists) `~/.kube/config` and paste the contents of the `k3s.yaml` output here.

Afterwards, you can test that `kubectl` works by running a command like `kubectl describe services`. It should not return any errors.


```bash
/Users/veselintonev/programming/Udacity/cloud_native_nd/udaConnect
$ vagrant ssh
Last login: Tue Oct  5 04:54:18 2021 from 10.0.2.2
vagrant@master:~> curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
vagrant@master:~> chmod 700 get_helm.sh
vagrant@master:~> ./get_helm.sh
Downloading https://get.helm.sh/helm-v3.7.0-linux-amd64.tar.gz
Verifying checksum... Done.
Preparing to install helm into /usr/local/bin
helm installed into /usr/local/bin/helm
vagrant@master:~> helm version
version.BuildInfo{Version:"v3.7.0", GitCommit:"eeac83883cb4014fe60267ec6373570374ce770b", GitTreeState:"clean", GoVersion:"go1.16.8"}
vagrant@master:~> helm repo add bitnami https://charts.bitnami.com/bitnami
"bitnami" has been added to your repositories
vagrant@master:~> exit
logout
Connection to 127.0.0.1 closed.
```

```bash
master:/home/vagrant # helm repo add bitnami https://charts.bitnami.com/bitnami
"bitnami" has been added to your repositories
master:/home/vagrant # export KUBECONFIG=/etc/rancher/k3s/k3s.yaml
master:/home/vagrant # helm install udaconnect-kafka bitnami/kafka
NAME: udaconnect-kafka
LAST DEPLOYED: Tue Oct  5 06:47:38 2021
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
** Please be patient while the chart is being deployed **

Kafka can be accessed by consumers via port 9092 on the following DNS name from within your cluster:

    udaconnect-kafka.default.svc.cluster.local

Each Kafka broker can be accessed by producers via port 9092 on the following DNS name(s) from within your cluster:

    udaconnect-kafka-0.udaconnect-kafka-headless.default.svc.cluster.local:9092

To create a pod that you can use as a Kafka client run the following commands:

    kubectl run udaconnect-kafka-client --restart='Never' --image docker.io/bitnami/kafka:2.8.1-debian-10-r0 --namespace default --command -- sleep infinity
    kubectl exec --tty -i udaconnect-kafka-client --namespace default -- bash

    PRODUCER:
        kafka-console-producer.sh \

            --broker-list udaconnect-kafka-0.udaconnect-kafka-headless.default.svc.cluster.local:9092 \
            --topic test

    CONSUMER:
        kafka-console-consumer.sh \

            --bootstrap-server udaconnect-kafka.default.svc.cluster.local:9092 \
            --topic test \
            --from-beginning
master:/home/vagrant # 
```

```bash
master:/home/vagrant # kubectl get pods
NAME                           READY   STATUS    RESTARTS   AGE
udaconnect-kafka-zookeeper-0   1/1     Running   0          48m
udaconnect-kafka-0             1/1     Running   0          48m
master:/home/vagrant #
```

### Steps
1. `kubectl apply -f deployment/db-configmap.yaml` - Set up environment variables for the pods
2. `kubectl apply -f deployment/db-secret.yaml` - Set up secrets for the pods
3. `kubectl apply -f deployment/postgres.yaml` - Set up a Postgres database running PostGIS
4. `kubectl apply -f deployment/udaconnect-api.yaml` - Set up the service and deployment for the API
5. `kubectl apply -f deployment/udaconnect-app.yaml` - Set up the service and deployment for the web app
6. `sh scripts/run_db_command.sh <POD_NAME>` - Seed your database against the `postgres` pod. (`kubectl get pods` will give you the `POD_NAME`)

### Addition Steps
1. `kubectl apply -f deployment/consumer.yaml` - Set up the consumer service
2. `kubectl apply -f deployment/frontend.yaml` - Set up the frontend application
3. `kubectl apply -f modules/person/deployment/udaconnect-person.yaml` - Set up person api service
4. `kubectl apply -f modules/location_producer/deployment/location_producer.yaml` - Set up location_producer service
5. `kubectl apply -f modules/location_consumer/deployment/location_consumer.yaml` - Set up location_consumer service

Manually applying each of the individual `yaml` files is cumbersome but going through each step provides some context on the content of the starter project. In practice, we would have reduced the number of steps by running the command against a directory to apply of the contents: `kubectl apply -f deployment/`.

Note: The first time you run this project, you will need to seed the database with dummy data. Use the command `sh scripts/run_db_command.sh <POD_NAME>` against the `postgres` pod. (`kubectl get pods` will give you the `POD_NAME`). Subsequent runs of `kubectl apply` for making changes to deployments or services shouldn't require you to seed the database again!

### Verifying it Works
Once the project is up and running, you should be able to see 3 deployments and 3 services in Kubernetes:
`kubectl get pods` and `kubectl get services` - should both return `udaconnect-app`, `udaconnect-api`, and `postgres`


These pages should also load on your web browser:
* `http://localhost:30001/` - OpenAPI Documentation
* `http://localhost:30001/api/` - Base path for API
* `http://localhost:30000/` - Frontend ReactJS Application

#### Deployment Note
You may notice the odd port numbers being served to `localhost`. [By default, Kubernetes services are only exposed to one another in an internal network](https://kubernetes.io/docs/concepts/services-networking/service/). This means that `udaconnect-app` and `udaconnect-api` can talk to one another. For us to connect to the cluster as an "outsider", we need to a way to expose these services to `localhost`.

Connections to the Kubernetes services have been set up through a [NodePort](https://kubernetes.io/docs/concepts/services-networking/service/#nodeport). (While we would use a technology like an [Ingress Controller](https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/) to expose our Kubernetes services in deployment, a NodePort will suffice for development.)

## Development
### New Services
New services can be created inside of the `modules/` subfolder. You can choose to write something new with Flask, copy and rework the `modules/api` service into something new, or just create a very simple Python application.

As a reminder, each module should have:
1. `Dockerfile`
2. Its own corresponding DockerHub repository
3. `requirements.txt` for `pip` packages
4. `__init__.py`

### Docker Images
`udaconnect-app` and `udaconnect-api` use docker images from `isjustintime/udaconnect-app` and `isjustintime/udaconnect-api`. To make changes to the application, build your own Docker image and push it to your own DockerHub repository. Replace the existing container registry path with your own.

## Configs and Secrets
In `deployment/db-secret.yaml`, the secret variable is `d293aW1zb3NlY3VyZQ==`. The value is simply encoded and not encrypted -- this is ***not*** secure! Anyone can decode it to see what it is.
```bash
# Decodes the value into plaintext
echo "d293aW1zb3NlY3VyZQ==" | base64 -d

# Encodes the value to base64 encoding. K8s expects your secrets passed in with base64
echo "hotdogsfordinner" | base64
```
This is okay for development against an exclusively local environment and we want to keep the setup simple so that you can focus on the project tasks. However, in practice we should not commit our code with secret values into our repository. A CI/CD pipeline can help prevent that.

## PostgreSQL Database
The database uses a plug-in named PostGIS that supports geographic queries. It introduces `GEOMETRY` types and functions that we leverage to calculate distance between `ST_POINT`'s which represent latitude and longitude.

_You may find it helpful to be able to connect to the database_. In general, most of the database complexity is abstracted from you. The Docker container in the starter should be configured with PostGIS. Seed scripts are provided to set up the database table and some rows.
### Database Connection
While the Kubernetes service for `postgres` is running (you can use `kubectl get services` to check), you can expose the service to connect locally:
```bash
kubectl port-forward svc/postgres 5432:5432
```
This will enable you to connect to the database at `localhost`. You should then be able to connect to `postgresql://localhost:5432/geoconnections`. This is assuming you use the built-in values in the deployment config map.
### Software
To manually connect to the database, you will need software compatible with PostgreSQL.
* CLI users will find [psql](http://postgresguide.com/utilities/psql.html) to be the industry standard.
* GUI users will find [pgAdmin](https://www.pgadmin.org/) to be a popular open-source solution.

## Architecture Diagrams
Your architecture diagram should focus on the services and how they talk to one another. For our project, we want the diagram in a `.png` format. Some popular free software and tools to create architecture diagrams:
1. [Lucidchart](https://www.lucidchart.com/pages/)
2. [Google Docs](docs.google.com) Drawings (In a Google Doc, _Insert_ - _Drawing_ - _+ New_)
3. [Diagrams.net](https://app.diagrams.net/)

## Tips
* We can access a running Docker container using `kubectl exec -it <pod_id> sh`. From there, we can `curl` an endpoint to debug network issues.
* The starter project uses Python Flask. Flask doesn't work well with `asyncio` out-of-the-box. Consider using `multiprocessing` to create threads for asynchronous behavior in a standard Flask application.
