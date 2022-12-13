---
layout: page
title: Helm
menubar: cheatsheets_menu
hide_footer: true
show_sidebar: false
toc: true
---

<img align="center" width="20%" src="../../images/helm-logo.png">

## Download and Install

```
wget https://get.helm.sh/helm-v3.10.2-linux-amd64.tar.gz

tar xvzf helm-v3.10.2-linux-amd64.tar.gz

mv linux-amd64/helm $HOME/.local/bin/
chmod +x $HOME/.local/bin/helm

helm version
version.BuildInfo{Version:"v3.10.2", GitCommit:"50f003e5ee8704ec937a756c646870227d7c8b58", GitTreeState:"clean", GoVersion:"go1.18.8"}
```

## Overview

Helm is an open source tool used for packaging and deploying applications on 
Kubernetes. It is often referred to as the Kubernetes package manager because of its
similarities to any other package manager you would find on your favorite operating system (OS).
Helm is widely used throughout the Kubernetes community and is a CNCF graduated project.

Helm was designed to provide an experience similar to that of package managers.
Taking as example **dnf** or similar tools will immediately understand Helm’s basic concepts. 
Dnf operates on **RPM Package Manager (RPM)** packages that provide executables, 
dependency information, and metadata. Helm, on the other hand, works with **charts**. 
A Helm chart can be thought of as a Kubernetes package. Charts contain the 
declarative Kubernetes resource files required to deploy an application. Similar to an
RPM package, it can also declare one or more dependencies that the application needs in order to run.
Helm relies on repositories (such as RPM repositories) to provide widespread 
access to charts. Chart developers create declarative YAML files, package them 
into charts, and publish them to chart repositories. End users then use Helm to 
search for existing charts to deploy onto Kubernetes, similar to how end users 
of dnf will search for RPM packages to deploy to Fedora for example.

The best way of understanding helm is to take a look to a simple user session.

### Simple session

**Note**: Helm interacts directly with the Kubernetes API server. For that reason, Helm needs
to be able to connect to a Kubernetes cluster. Helm attempts to do this automatically
by reading the same configuration files used by kubectl (the main Kubernetes
command-line client).  Helm will try to find this information by reading the environment variable
$KUBECONFIG. If that is not set, it will look in the same default locations that
kubectl looks in (for example, $HOME/.kube/config on UNIX, Linux, and macOS).
You can also override these settings with environment variables (HELM_KUBECONTEXT)
and command-line flags (--kube-context). You can see a list of environment vari‐
ables and flags by running helm help.

For example Helm can be used to deploy Redis, an in-memory cache, to Kubernetes
by using a chart from an upstream repository. This can be performed using Helm’s install command,
as illustrated here:

```
helm install redis bitnami/redis
or
helm install redis bitnami/redis --namespace=redis (already created namespace)
```

This would install the redis chart from the **bitnami repository** to Kubernetes.
The output to this command will be instructions for the end user, the information
displayed depends on the helm chart creator.

```
NAME: redis
LAST DEPLOYED: Sun Dec 11 10:55:37 2022
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
CHART NAME: redis
CHART VERSION: 17.3.13
APP VERSION: 7.0.5

To get your password run:
    export REDIS_PASSWORD=$(kubectl get secret --namespace default redis -o jsonpath="{.data.redis-password}" | base64 -d)
[...]
```

If a new version of the redis chart becomes available, users can upgrade to the new version using
the upgrade command, as follows:

```
helm upgrade redis bitnami/redis
```

With OSs, users should be concerned about rollbacks if a bug or vulnerability is found. The same
concern exists with applications on Kubernetes, and Helm provides the rollback command to
handle this use case, as illustrated here:

```
helm rollback redis 1
```

This command would roll redis back to its first revision. Helm has a concept 
called **release history**. When a Helm chart is installed for the first time, 
Helm adds that initial revision to the history. The history is further modified 
as revisions increase via upgrades, keeping various snapshots of how the 
application was configured at varying revisions.

Finally, Helm provides the ability to remove redis altogether with the uninstall command, as
follows:

```
helm uninstall redis
```

Let's compare a OS's package manager such as DNF and HELM in the following table:

| dnf commands    | helm commands |  Description      |
|-----------------|---------------|-------------------|
| dnf install     | helm install  | Install an application and its dependencies |
| dnf upgrade     | helm upgrade  | Upgrade an application to a newer version. Upgrade dependencies as specified by the target package |
| dnf downgrade   | helm rollback | Revert an application to a previous version. Revert dependencies as specified by the target package |
| dnf remove      | helm uninstall| Delete an application. Each tool has a different philosophy around handling dependencies |


### Charts, Repositories and Releases

**Chart** is a Helm package. It contains all of the resource definitions necessary 
to run an application, tool, or service inside of a Kubernetes cluster. 
Think of it like the Kubernetes equivalent of a Homebrew formula, an Apt dpkg, 
or a Yum RPM file.

**Repository** is the place where charts can be collected and shared. It's like 
Perl's CPAN archive or the Fedora Package Database, but for Kubernetes packages.

**Release** is an instance of a chart running in a Kubernetes cluster. One chart 
can often be installed many times into the same cluster. And each time it is 
installed, a new release is created. Consider a MySQL chart. If you want two 
databases running in your cluster, you can install that chart twice. Each one 
will have its own release, which will in turn have its own release name.

With these concepts in mind, we can now explain Helm like this:

Helm installs charts into Kubernetes, creating a new release for each installation. 
And to find new charts, you can search Helm chart repositories.

## Helm Workflow

In what follows, we are going to take a look at the most common workflow for start‐
ing out with Helm:

1. Add a chart repository.
2. Find a chart to install.
3. Install a Helm chart.
4. See the list of what is installed.
5. Upgrade your installation.
6. Delete the installation.


### 1 - Add a chart repository

A Helm chart is an individual package that can be installed into your Kubernetes clus‐
ter. During chart development, you will often just work with a chart that is stored on
your local filesystem.
But when it comes to sharing charts, Helm describes a standard format for indexing
and sharing information about Helm charts. A Helm chart repository is simply a set of
files, reachable over the network, that conforms to the Helm specification for index‐
ing packages.

There are many  repositories on the internet. The easiest way to find the popular 
repositories is to use your web browser to navigate to the [Artifact Hub](https://artifacthub.io/). 
There you will find thousands of Helm charts, each hosted on an appropriate repository.

**Note**: In Helm 3, there is no default repository. Users are encouraged to use the Artifact
Hub to find what they are looking for and then add their preferred repositories.

Adding a Helm chart is done with the ```helm repo add``` command. Several Helm
repository commands are grouped under the helm repo command group:

```
helm repo add bitnami https://charts.bitnami.com/bitnami
"bitnami" has been added to your repositories
```

Now we can verify that the Bitnami repository exists by running a second repo
command:

```
helm repo list
NAME            URL                                                        
bitnami         https://charts.bitnami.com/bitnami                         
harbor          https://helm.goharbor.io                                   
incubator       https://charts.helm.sh/incubator                           
spark-operator  https://googlecloudplatform.github.io/spark-on-k8s-operator
```

### 2 - Find a chart to install

Once we have added a repository, its index will be locally cached until we next update
it. And one important thing that we can now do is search the repository.

```
helm search repo drupal
NAME            CHART VERSION   APP VERSION     DESCRIPTION                                       
bitnami/drupal  12.5.10         9.4.8           Drupal is one of the most versatile open source...

helm search repo content
NAME                            CHART VERSION   APP VERSION     DESCRIPTION                                       
bitnami/drupal                  12.5.10         9.4.8           Drupal is one of the most versatile open source...
bitnami/harbor                  16.0.4          2.6.2           Harbor is an open source trusted cloud-native r...
bitnami/joomla                  12.0.2          4.0.5           PHP content management system (CMS) for publish...
bitnami/mongodb                 10.31.5         4.4.11          NoSQL document-oriented database that stores JS...
bitnami/mongodb-sharded         3.12.0          4.4.11          NoSQL document-oriented database that stores JS...
bitnami/nginx                   13.2.14         1.23.2          NGINX Open Source is a web server that can be a...
bitnami/owncloud                12.2.7          10.11.0         ownCloud is an open source content collaboratio...
bitnami/wordpress               15.2.17         6.1.1           WordPress is the world's most popular blogging ...
harbor/harbor                   1.10.2          2.6.2           An open source trusted cloud native registry th...
```
We did a simple search for the term drupal. Helm will search not just the package
names, but also other fields like labels and descriptions. Thus, we could search for
content and see Drupal listed there because it is a content management system.

By default, Helm tries to install the latest stable release of a chart, but you can over‐
ride this behavior and install a specific verison of a chart. Thus it is often useful to see
not just the summary info for a chart, but exactly which versions exist for a chart:

```
helm search repo drupal --versions                                                                                            
NAME            CHART VERSION   APP VERSION     DESCRIPTION
bitnami/drupal  12.5.10         9.4.8           Drupal is one of the most versatile open source...
bitnami/drupal  12.5.9          9.4.8           Drupal is one of the most versatile open source...
bitnami/drupal  12.5.8          9.4.8           Drupal is one of the most versatile open source...
bitnami/drupal  12.5.7          9.4.8           Drupal is one of the most versatile open source...
bitnami/drupal  12.5.6          9.4.8           Drupal is one of the most versatile open source...
```

**Note**: A chart version is the version of the Helm chart. The app version is
the version of the application packaged in the chart. Helm uses the
chart version to make versioning decisions, such as which package
is newest. Multiple chart versions may contain the same app version.

### 3 - Install a Helm chart

At very minimum, installing a chart in Helm requires just two pieces of information:
the name of the installation and the chart you want to install.

we distinguished between an installation and a
chart. This is an important distinction during installation and upgrading. In an oper‐
ating system package manager, we may request that it install a piece of software. But
it is extremely rare that we need to install the same exact package multiple times on
an operating system. A Kubernetes cluster is different. It makes complete sense in
Kubernetes to say “I want to install a MySQL database for Application A, and a sec‐
ond MySQL database for Application B.” Even if the two databases are exactly the
same version and have the same configuration, in order to appropriately manage our
applications, we may desire to have two instances running.

Therefore, Helm needs a way to distinguish between the different instances of the
same chart. So an installation of a chart is a specific instance of the chart. One chart
may have many installations. When we run the helm install command, we need to
give it an installation name as well as the chart name. So the most basic installation
command looks something like this:

```
helm install mysite bitnami/drupal
```

For example, the following is perfectly legal in Helm 3:

```
kubectl create ns first
kubectl create ns second
helm install --namespace first mysite bitnami/drupal
helm install --namespace second mysite bitnami/drupal
```
Many charts will allow you to provide configuration values (the available values
are listed in the documentation of the chart). We can inspect the modificable values
with the following command:

```
helm show values bitnami/drupal
```
There are several ways of telling 
Helm which values you want to be configured. The best way is to create a YAML 
file with all of the configuration overrides.

```
helm install mysite bitnami/drupal --values values.yaml
```

There is a second flag that can be used to add individual parameters to an install or
upgrade.

```
helm install mysite bitnami/drupal --set drupalUsername=admin
```
Configuration parameters can be structured. That is, a configuration file may have
multiple sections. The Drupal chart, for example, has configuration specific to the
MariaDB database. These parameters are all grouped into a mariadb section. Building
on our previous example, we could override the MariaDB database name like this:

```
drupalUsername: admin
drupalEmail: admin@example.com
mariadb:
    db:
        name: "my-database"
```

Subsections are a little more complicated when using the --set flag. You will need to
use a dotted notation: --set mariadb.db.name=my-database. This can get verbose
when setting multiple values.

**Note**: In general, Helm core maintainers suggest storing configuration in values.yaml files
(note that the filename does not need to be “values”), only using --set when abso‐
lutely necessary.


### 4 - See the list of what is installed

```
helm list
helm list --all-namespaces
```

### 5 - Upgrade your installation

When we talk about upgrading in Helm, we talk about upgrading an installation, not
a chart. An installation is a particular instance of a chart in your cluster. When you
run helm install, it creates the installation. To modify that installation, use helm
upgrade.

This is an important distinction to make in the present context because upgrading an
installation can consist of two different kinds of changes:

* You can upgrade **the version of the chart**
* You can upgrade **the configuration of the installation**

The two are not mutually exclusive; you can do both at the same time. But this does
introduce one new term that Helm users refer to when talking about their systems: **a
release** is a particular combination of configuration and chart version for an
installation.

When we first install a chart, we create the initial release of an installation. Let’s call
this release 1. When we perform an upgrade, we are creating a new release of the
same installation: release 2. When we upgrade again, we will create release 3.

During an upgrade, then, we can create a release with new configuration, with a new
chart version, or with both.

* Example of changing only the configuration:

```
helm install mysite bitnami/drupal --set ingress.enabled=false
helm upgrade mysite bitnami/drupal --set ingress.enabled=true
```

The preceding example install durpal and we do an upgrade of the configuration 
that it will only change the ingress configuration. Nothing changes with the 
database, or even with the web server running Drupal. For that reason, nothing 
will be restarted or deleted and re-created. Helm will load the chart, generate 
all of the Kubernetes objects in that chart, and then see how those differ from 
the version of the chart that is already installed. It will only send Kubernetes 
the things that need to change. In other words, Helm will attempt to alter only 
the bare minimum.

On occasion, you may want to force one of your services to restart. This is not some‐
thing you need to use Helm for. You can simply use kubectl itself to restart things.
With an operating system’s package manager, you do not use the package manager to
restart a program.


* Example of changing to a new chart:

When a new version of a chart comes out, you may want to upgrade your existing
installation to use the new chart version.

```
helm repo update
helm upgrade mysite bitnami/drupal
```

As you can see, the default policy of Helm is to attempt to use the latest version of a
chart. If you would prefer to stay on a particular version of a chart, you can explicitly
declare this:

```
helm upgrade mysite bitnami/drupal --version 6.2.22
```

**Note**: Take a look to this example

```
helm install mysite bitnami/drupal --values values.yaml
helm repo update
helm upgrade mysite bitnami/drupal
```
What is the result of this pair of operations? The installation will use all of the config‐
uration data supplied in values.yaml, but the upgrade will not. As a result, some set‐
tings could be changed back to their defaults.

Helm core maintainers suggest that you provide consistent configuration with each
installation and upgrade. To apply the same configuration to both releases, supply the
values on each operation.

```
helm install mysite bitnami/drupal --values values.yaml
helm repo update
helm upgrade mysite bitnami/drupal --values values.yaml
```

There is an upgrade shortcut available that will just reuse the last set of val‐
ues that you sent.

```
helm upgrade mysite bitnami/drupal --reuse-values
```

The --reuse-values flag will tell Helm to reload the server-side copy of the last set of
values, and then use those to generate the upgrade. This method is okay if you are
always just reusing the same values. However, the Helm maintainers strongly suggest
not trying to mix --reuse-values with additional --set or --values options.

### 6 - Delete the installation

```
helm uninstall mysite
helm uninstall mysite --namespace first
```

## Helm configuration

Helm is a tool with sensible defaults that allow users to be productive without 
needing to perform a large number of tasks post-installation. With that being 
said, there are several different options users can change or enable to modify 
Helm’s behavior.

Helm relies on the existence of environment variables to configure some of the 
low-level options. There are many variables you can configure, each of which 
can be seen in the helm help output.

A few environment variables are used for storing Helm metadata:

* HELM_CACHE_HOME or XDG_CACHE_HOME: Sets an alternative location for storing cached files
* HELM_CONFIG_HOME or XDG_CONFIG_HOME: Sets an alternative location for storing
Helm configuration
* HELM_DATA_HOME or XDG_DATA_HOME: Sets an alternative location for storing Helm data

Helm adheres to the [XDG Base Directory Specification](https://specifications.freedesktop.org/basedir-spec/basedir-spec-latest.html), which is designed to provide a standardized
way of defining where different files are located on an operating system’s filesystem.

Helm uses the cache path to store charts that are downloaded from upstream chart repositories.
Installed charts are cached to the local machine to enable faster installation of the chart the next time
it is referenced.

The configuration path is used to save repository information, such as the URL and credentials for
authentication, if required.

The data path is used to store plugins. When a plugin is installed using the helm plugin install
command, the plugin itself is stored in this location.

The defaults are:

| Cache Path    | Configuration Path |  Data Path |
|-----------------|---------------|-------------------|
| $HOME/.cache/helm | $HOME/.config/helm  | $HOME/.local/share/helm |

Besides the Helm metadata paths, other environment variables are used for configuring regular
Helm usage:

* HELM_DRIVER: Sets the backend storage driver. It is used to determine how the release
state is stored in Kubernetes. The default value is secret, which Base64-encodes the state
of a release in a Kubernetes secret. Other options include configmap, which stores state
in a plaintext Kubernetes ConfigMap, memory, which stores the state in the local process’s
memory, and sql, which stores state in a relational database.
* HELM_NAMESPACE: Sets the namespace that’s used for Helm operations. The HELM_
NAMESPACE environment variable is used to set the namespace in which Helm operations
take place. This is a convenient environment variable to use as it prevents you from needing
to pass the --namespace or -n flag on each Helm invocation.
* KUBECONFIG: Sets an alternative Kubernetes configuration file. The KUBECONFIG environment
variable is used to set the file that’s used for authentication to the Kubernetes cluster. If unset, the
default value will be ~/.kube/config. In most cases, users won’t need to modify this value.

## Helm Chart Development

### Chart Scaffolding

Helm includes the create command to make it easy for you to create a chart of your
own, and it’s a great way to get started. This command creates a new Nginx chart,
with a name of your choice, following best practices for a chart layout.

```
helm create example

example/
├── charts
├── Chart.yaml
├── templates
│   ├── deployment.yaml
│   ├── _helpers.tpl
│   ├── hpa.yaml
│   ├── ingress.yaml
│   ├── NOTES.txt
│   ├── serviceaccount.yaml
│   ├── service.yaml
│   └── tests
│       └── test-connection.yaml
└── values.yaml
```

Let's review the components newly created by scaffolding:

The **Chart.yaml** file contains metadata and some functionality controls for the
chart.

**.helmignore** A file that contains a list of files and directories that should be omitted from
the Helm chart’s packaging.

**charts/** A directory that contains dependencies or Helm charts that the parent chart
depends on.

**templates/** used to generate Kubernetes manifests are stored in the templates
directory. These templates are Golang templates.

**templates/NOTES.txt** file is a special template. When a chart is installed, the NOTES.txt
template is rendered and displayed rather than being installed into a cluster.

**templates/tests/** can include tests that are not installed as part of the install or
upgrade commands. This chart includes a test that is used by the helm test command.

**values.yaml** default values passed to the templates when Helm is rendering the manifests are
in the values.yaml file. When you instantiate a chart, these values can be overridden.

In addition to the files listed in the preceding table, there are a few other files that a Helm chart can contain
that helm create did not include for us. Let’s take a high-level look at these files:

**Chart.lock** A file used to save, or lock in, the No previously applied dependency versions.

**crds/** A directory that contains Custom Resource Definition (CRD) YAML resources. 
These CRD resources will be installed before those under templates/.

**README.md** A file that contains installation and usage information about the Helm chart.

**LICENSE** A file that contains the chart’s license, which provides information about
usage and redistribution rights.

**values.schema.json**: A file that contains the chart’s values schema in the JSON format. 
Used to provide input validation.

### Chart Validation

We can validate this new chart with different options:

1. ```helm lint .```: Is your go-to tool for verifying that your chart follows best practices
2. ```helm template --debug .```: Will test rendering chart templates locally.
3. ```helm install --dry-run --debug .```: The server render your templates, then return the resulting manifest file.
4. ```helm get manifest .```: To see what templates are installed on the server.


### Chart Installation

You can install this newly created chart without any modifications by running 
the following command:

```
helm install myapp example
```

When you run this command Helm will create an instance of the chart running in the
cluster with the name myapp. It will install it using the currently configured connec‐
tion and context you use for Kubernetes. Helm is using the same configuration you’re
using when you use kubectl, the command-line application for Kubernetes. In that
command the final argument of example is the directory where the chart is located.

### Chart Publishing

The following is a session where we are to publish our helm package into GitHub
repository (with GitHub Pages enabled for downloading the charts):

```
helm package example

mv example-0.1.0.tgz /path/to/gitpagesrepo/
cd /path/to/gitpagesrepo/

helm repo index .

git add . && git commit -m "upload new chart"

helm repo add kubernetesbigdataeg https://kubernetesbigdataeg.github.io/charts/

helm repo list
NAME                    URL                                                        
bitnami                 https://charts.bitnami.com/bitnami                         
harbor                  https://helm.goharbor.io                                   
incubator               https://charts.helm.sh/incubator                           
spark-operator          https://googlecloudplatform.github.io/spark-on-k8s-operator
kubernetesbigdataeg     https://kubernetesbigdataeg.github.io/charts/     

helm search repo example
NAME                            CHART VERSION   APP VERSION     DESCRIPTION                
kubernetesbigdataeg/example     0.1.0           1.16.0          A Helm chart for Kubernetes

helm install my-example kubernetesbigdataeg/example 
NAME: my-example
LAST DEPLOYED: Mon Dec 12 14:02:25 2022
NAMESPACE: default
STATUS: deployed
REVISION: 1
NOTES:
1. Get the application URL by running these commands:
  export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/name=example,app.kubernetes.io/instance=my-example" -o jsonpath="{.items[0].metadata.name}")
  export CONTAINER_PORT=$(kubectl get pod --namespace default $POD_NAME -o jsonpath="{.spec.containers[0].ports[0].containerPort}")
  echo "Visit http://127.0.0.1:8080 to use your application"
  kubectl --namespace default port-forward $POD_NAME 8080:$CONTAINER_PORT

k get po
NAME                                      READY   STATUS    RESTARTS        AGE
my-example-65c94d55fc-jzdhs               1/1     Running   0               39s

helm list
NAME            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART           APP VERSION
my-example      default         1               2022-12-12 14:02:25.13897309 +0100 CET  deployed        example-0.1.0   1.16.0     

helm get manifest my-example
```

