---
title: Install Service Catalog
approvers:
- chenopis
---

{% capture overview %}
{% include templates/glossary/snippet.md term="service-catalog" length="long" %}

Use the [Service Catalog Installer](https://github.com/GoogleCloudPlatform/k8s-service-catalog#installation) tool to easily install or uninstall Service Catalog on your Kubernetes cluster. This CLI tool is installed as `sc` in your local environment.

{% endcapture %}


{% capture prerequisites %}
* Understand key concepts of [Service Catalog](/docs/concepts/service-catalog/).
* Install [Go 1.6+](https://golang.org/dl/) and set the `GOPATH`.
* Install the [cfssl](https://github.com/cloudflare/cfssl) tool needed for generating SSL artifacts. 
* Service Catalog requires Kubernetes version 1.7+.
* [Install and setup kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) so that it is configured to connect to a Kubernetes v1.7+ cluster.
* The kubectl user must be bound to the *cluster-admin* role for it to install Service Catalog. To ensure that this is true, run the following command:

        kubectl create clusterrolebinding cluster-admin-binding --clusterrole=cluster-admin --user=<user-name>

{% endcapture %}


{% capture steps %}
## Install `sc` in your local environment

Install the `sc` CLI tool using the `go get` command:

```Go
go get github.com/GoogleCloudPlatform/k8s-service-catalog/installer/cmd/sc
```

After running the above command, `sc` should be installed in your `GOPATH/bin` directory.

## Install Service Catalog in your Kubernetes cluster

First, verify that all dependencies have been installed. Run:

```shell
sc check
```

If the check is successful, it should return: 

```
Dependency check passed. You are good to go.
``` 

Next, run the install command and specify the `storageclass` that you want to use for the backup:

```shell
sc install --etcd-backup-storageclass "standard"
```

## Uninstall Service Catalog

To uninstall Service Catalog from your Kubernetes cluster using the `sc` tool, run:

```shell
sc uninstall
```

That should remove Service Catalog from your Kubernetes cluster.

{% endcapture %}


{% capture whatsnext %}
* Learn how to [provision and bind an external Service with Service Catalog](/docs/tasks/service-catalog/provision-bind-external-service/).

{% endcapture %}


{% include templates/task.md %}