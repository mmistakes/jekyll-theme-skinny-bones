# Developer Driven DevOps as a Learning Tool
## A simple approach to GKE clusters as immutable infrastructure
---
###### May 4, 2018

### Background
A few weeks ago we were looking to provision Kubernetes clusters for a new client project. 
We’re using Google Kubernetes Engine, so a lot of the heavy lifting of setting up Kubernetes is taken care of. 
There are still a few things that need to be handled – creating the project, setting up billing, the actual 
creation of the clusters, managing the GCP service accounts & keys. These tasks are pretty straightforward. 

While learning how to do what we wanted to do we came across a number of how-to guides and getting started 
tutorials that we wanted to steal bits and pieces of. We wanted to make sure we had some discipline to managing 
the configuration of our clusters, and could re-build from scratch if we needed to wipe the slate clean and start over. 

During this time I had also started to look into Google container builders. Using containers for build steps has some 
attractive features. The environment is encapsulated - if a particular tool or version of a library is needed for a
build step, it can be set up in the Docker image and re-used wherever. The only local dependency is Docker; if you 
can run a Docker container, you can execute the build step. There are many options for composing the steps into a 
pipeline – TravisCI, Circle CI, Docekr compose, and Google container builder itself. For a lot of the things we want 
to do, their already exist builder images for common tasks – gcloud, kubectl, docker, npm – as well as a community for 
others such as helm and docker-compose.   

We came across a few tools for declarative administration of kubernetes clusters: 
- [Kubespray](https://github.com/kubernetes-incubator/kubespray)
- [Kops](https://github.com/kubernetes/kops)
- [Kubeadm](https://github.com/kubernetes/kubeadm)
- [Terraform](https://www.terraform.io/)

We wanted to use the gcloud / GKE client & APIs more before we make an informed assessment of these tools. 
There is some overlap between the scope of these tools and the PaaS offering of GKE. We’ll be keeping an eye 
on these tools and applying them to our projects as makes sense, but for now we’re limiting ourselves to the 
gcloud client and kubectl. 

Applying all of this to our project, we developed a pattern for a tool for administration of our Kubernetes clusters: 
-	Based on existing container builders
-	Use GKE (google kubernetes engine)
-	Opinionated builders dedicated to specific cluster instances
-	Extensible for new projects
-	Version controlled, scripted cluster creation
-	Learn to use gcloud / kubectl clients and share learning with team

### Ramp
An example of this can be found on Github in the UnspecifiedLLC/ramp project, which we’ll look at now. 

The Ramp base image provides the scripts, but it does not contain any configuration. To use ramp, build a new image from ramp and provide a CONF_DIR build argument. Your image will scan that directory for folders that are your environments. 


### Testramp
The ramp project container a Dockerfile.testramp you can build to try out ramp. All it does is define the environments for two hypothetical clusters we want to provision - ramp_sample_dev and ramp_sample_prod. 

So what can you do with testramp? There are three main pieces: The ‘up’ command, the ‘down’ command, and the entrypoint script. 

Let’s start with entrypoint. Entrypoint is – surprisingly – the entrypoint of the docker image. 

### Entrypoint
Entrypoint parses the command line arguments and maps them onto subcommands. If you run the image with no arguments 
(or with the ‘usage’ subcommand) you’ll get a summary of how to use testramp:  

```console
$ docker run -it --rm testramp usage
Usage: Docker run -it --rm [service key] image [environment name] command
  service key       should be a base64 encoded string of a JSON service account key. It should be passed to the docker
                    image as the environment variable GCLOUD_SERVICE_KEY:
                    -e GCLOUD_SERVICE_KEY=<value>
  image             reference to this Ramp Docker image
  environment name  should be one of the environment configurations included in this Ramp container.
  command may be    help | list-env | usage | up | down | info
                     help: how to use this Ramp image
                     list-env: list the environment configurations included in this Ramp container.
                     usage: this message
                     up: create the Kubernetes cluster as per the environment configuration. Requires a valid environment name.
                     down: destroy the Kubernetes cluster for the named environment configuration
                     info: current status / information about environment resources
```

Entrypoint documents how to use the provided scripts, and helps the user to discover what options are available to them. 

```console
$ docker run -it --rm testramp list-env
Available environments:
  ramp_sample_dev
  ramp_sample_prod
```

The ‘help’, ‘usage’, and ‘list-env’ commands do not actually interact with cloud resources, and can be run without providing an environment name or service account credentials. Entrypoint provides structure to useful or frequently used commands, reduces the opportunity to mess up, and gives immediate usage feedback to the user. 

### Authenticated Commands
The commands implemented in testramp are to 1) create a cluster if it does not already exist (up); 2) destroy a cluster (down); and 3) to dump configuration information for an environment (info). In this example, these commands will do pretty much what will happen if you use the GKE console or API. However, a user can inspect the scripts to see how to do these tasks, and the scripts can be extended to do additional configuration.

These commands will require a service account and the name of one of the configured environments. Running one of these commands without a service account will not work - but testramp will tell you why:

```console
$ docker run -it --rm testramp ramp_sample_dev up
WARNING: Property [project] is overridden by environment setting [CLOUDSDK_CORE_PROJECT=testramp-dev]
Updated property [core/project].
Updated property [compute/zone].
Updated property [container/cluster].
    No gcloud service account is active.
    A service account key is provided by base64 encoding a a json service key, and passing it as an environment variable:
    GCLOUD_SERVICE_KEY=<base64 encoded Google service key>

gcloud configuration failed
```
#### Up example
Up is used to provision Kubernetes clusters. The configuration options for each cluster is baked into the testramp image. Immediate feedback of available environments is provided when running the command:   

```console
$ docker run -e GCLOUD_SERVICE_KEY=$GCLOUD_SERVICE_KEY -it --rm testramp bogus_environment up
bogus_environment configuration does not exist
Available environments:
  ramp_sample_dev
  ramp_sample_prod
```

### Testramp Operations
  
Bringing it all together, this command will use testramp to create a new cluster, using the configuration from /conf/ramp_sample_dev/env.list: 

```console
$ docker run -e GCLOUD_SERVICE_KEY=$GCLOUD_SERVICE_KEY -it --rm testramp ramp_sample_dev up
```

Up will use the gcloud client to create a new cluster, if one does not already exist. For this to actually work, you’ll need a GCloud account, a project named testramp-dev, set up billing, and create a service account with necessary privileges & download. 

### So what does testramp accomplish?  
-	Opinionated tool for cluster instance provisioning
    * Self documenting
    * Encapsulated environment; no local dependency other than Docker
-	Training / knowledge sharing of use of gcloud, kubectl terminal clients
    * Team can develop recipes for common or useful tasks, refer to scripts for how to do things
-	Simple pattern for creating new instances for new environments / new projects
-	Get used to using gcloud service accounts / managing SA keys

Testramp is a useful exercise for us. This implementation is naïve; it does not do more than gcloud client with the options defined in the config. We’re using it as a first step into developer driven DevOps, putting the tools for provisioning clusters in the hands of the development team. It scripts cluster creation & maintains those scripts in version control; and treats our GKE clusters as immutable infrastructure.

Our next step is to evaluate more powerful tools for cluster administration. Terraform seems like a pretty good match for us. In my next post, we’ll go over what we learn.
