---
title: Service Catalog
approvers:
- chenopis
---

{% capture overview %}
{% include templates/glossary/snippet.md term="service-catalog" length="all" %}

{% endcapture %}


{% capture body %}
## Example use case

An [Application Developer](/docs/reference/glossary/?user-type=true#term-application-developer) wants to use a datastore, such as MySQL, as part of their application running in a Kubernetes cluster. However, they do not want to deal with the overhead of setting one up and administrating it themselves. Fortunately, there is a cloud provider that offers MySQL databases as a *Service* via a *Service Broker*.

A *Service Broker*, as defined by the [Open Service Broker API spec](https://github.com/openservicebrokerapi/servicebroker/blob/v2.13/spec.md), is an endpoint that manages the lifecycle of a set of Services, where a *Service* is a managed software offering that can be used by an application and is typically available via HTTP REST endpoints.

Using Service Catalog, the application developer can browse the list of Services through the Service Broker, provision a MySQL database instance, and bind with it to get the configuration and credentials necessary for the application to utilize the database.

## Architecture

Service Catalog is built on the [Open Service Broker API](https://github.com/openservicebrokerapi/servicebroker) and consists of its own API Server and Controller, outside of the Kubernetes Core. It communicates with Service Brokers via the OSB API and serves as an intermediary for the Kubernetes API Server in order to negotiate the initial provisioning and return the information needed for the application to use the Service.  

![Service Catalog Architecture](/images/docs/service-catalog-architecture.svg)

The *Service Consumer* is the application deployed within Kubernetes that connects with and uses the Service after Service Catalog has dealt with the initial provisioning and binding of the Service.

## Usage

The main Service Catalog operations that can be performed are:

* List -- Query the services available from a Service Broker.
* Provision -- Request that the Service Broker create a new instance of the Service.
* Bind -- Request the information and credentials necessary to connect to and utilize the Service.
* Unbind -- Remove the established binding to the Service.
* Deprovision -- Notify the Service Broker to release and destroy the instance of the Service.

### Listing Services

The Open Service Broker API provides a `GET catalog` method to discover and list Services available from a Service Broker. Service Catalog uses this method to query and store that information as a local ServiceClass Resource, which is available to the Service Consumer.

The flow of API calls is:

1. The Catalog Operator must first add Broker Resources to the Service Catalog API Server. These resources identify available Service Brokers and point to their URL endpoints.
1. Service Catalog then requests a list of Services from the Service Broker.
1. The Service Broker returns a list of Services to the ServiceClass resource, which is created and persisted in the Service Catalog API Server.
1. The Service Consumer can then locally query the ServiceClass Resource for a list of available Services.

![List Services](/images/docs/service-catalog-list.svg){:height="80%" width="80%"}

### Setting up a Service

*Provision* and *Bind* are used, in that order, to contact a Service Broker, setup a new instance of a Service, and get the information necessary for the Service Consumer to connect with and utilize the Service.

#### Provisioning a new Service

1. The Service Consumer provisions a new instance by sending a POST command to the Service Catalog API Server, which creates and persists an Instance Resource.
1. Service Catalog then requests an instance from the Service Broker by sending an PUT command.
1. The Service Broker creates a new instance of the Service. 
1. If the creation was successful, the Service Broker returns an HTTP 200 response.
1. The Service Consumer can then check the status of the instance to see if it is ready.

![Provision a Service](/images/docs/service-catalog-provision.svg){:height="80%" width="80%"}

#### Binding to a Service

1. The Service Consumer requests a binding to the instance by sending a POST command to the Service Catalog API Server, which creates and persists a Binding Resource.
1. Service Catalog in turn requests a binding from the Service Broker using a PUT command.
1. The Service Broker then returns provider-specific information, such as coordinates, credentials, configs, necessary for Kubernetes to connect and access the Service instance.
1. The binding information and credentials are delivered to the Kubernetes API Server as a set of Kubernetes objects, such as a Service, Secret, ConfigMap, or Pod Preset.

![Bind to a Service](/images/docs/service-catalog-bind.svg){:height="80%" width="80%"}


### Performing cleanup

Once a Service instance is no longer needed, it can be released and cleaned up by using  the *Unbind* and *Deprovision* operations.

#### Unbinding the instance

1. The Service Consumer sends a DELETE command to Service Catalog API Server.
1. Service Catalog in turn sends a DELETE command to the Service Broker.
1. The Service Broker performs any relevant business logic and removes the binding.
1. The Service Broker returns an HTTP 202 response, which triggers the Service Catalog Controller to remove any corresponding binding resources from Kubernetes and run any finalization tasks.
1. The Service Catalog API Server deletes the Binding Resource and returns a response to the Service Consumer.

![Unbind the instance](/images/docs/service-catalog-unbind.svg){:height="80%" width="80%"}

#### Deprovisioning the instance

1. The Service Consumer can then send a DELETE command to the Service Catalog API Server to deprovision the Service instance.
1. Service Catalog in turn sends a DELETE command to the Service Broker.
1. The Service Broker deletes the Service instance and performs its own cleanup procedure.
1. The Service Broker returns an HTTP 202 response, which triggers the Service Catalog Controller run any finalization tasks.
1. The Service Catalog API Server deletes the Instance Resource and returns a response to the Service Consumer.

![Deprovision the instance](/images/docs/service-catalog-deprovision.svg){:height="80%" width="80%"}

## API Resources

The Service Catalog API provides the following Kubernetes resource types:

* `ServiceBroker`: An in-cluster representation of a broker server. A resource of this
type encapsulates connection details for that broker server. These are created
and managed by cluster operators who wish to use that broker server to make new
types of managed services available within their cluster.
* `ServiceClass`: A *type* of managed service offered by a particular broker.
Each time a new `ServiceBroker` resource is added to the cluster, the service catalog
controller connects to the corresponding broker server to obtain a list of
service offerings. A new `ServiceClass` resource will automatically be created
for each.
* `ServiceInstance`: A provisioned instance of a `ServiceClass`. These are created
by cluster users who wish to make a new concrete *instance* of some *type* of
managed service to make that available for use by one or more in-cluster
applications. When a new `ServiceInstance` resource is created, the service catalog
controller will connect to the appropriate broker server and instruct it to
provision the service instance.
* `ServiceBinding`: Access credential to a `ServiceInstance`. These
are created by cluster users who wish for their applications to make use of a
service `ServiceInstance`. Upon creation, the service catalog controller will
create a Kubernetes `Secret` containing connection details and credentials for
the service instance. Such `Secret`s can be mounted into pods as usual.

{% endcapture %}


{% capture whatsnext %}
* [Install Service Catalog](/docs/tasks/service-catalog/install-service-catalog/) in your Kubernetes cluster.
* Learn how to [provision and bind an external Service with Service Catalog](/docs/tasks/service-catalog/provision-bind-external-service/).
* View [sample service brokers](https://github.com/openservicebrokerapi/servicebroker/blob/master/gettingStarted.md#sample-service-brokers).
* Explore the [kubernetes-incubator/service-catalog](https://github.com/kubernetes-incubator/service-catalog) project.

{% endcapture %}


{% include templates/concept.md %}
