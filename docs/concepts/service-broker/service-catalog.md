---
title: Service Catalog
approvers:
- chenopis
---

{% capture overview %}
{% include templates/glossary/snippet.md term="service-catalog" length="long" %}

{% endcapture %}


{% capture body %}
## Example use case

An [Application Developer](/docs/reference/glossary/?user-type=true#term-application-developer) wants to use a datastore, such as MySQL, as part of their application running in a Kubernetes cluster. However, they do not want to deal with the overhead of setting one up and administrating it themselves. Fortunately, there is a cloud provider that offers MySQL databases as a *Service* via a *Service Broker*.

A *Service Broker*, as defined by the [Open Service Broker API spec](https://github.com/openservicebrokerapi/servicebroker/blob/v2.13/spec.md), is an endpoint that manages the lifecycle of a set of Services, where a *Service* is a managed software offering that can be used by an application and is typically available via HTTP REST endpoints.
{: .note}

Using Service Catalog, the application developer can browse the list of Services through the Service Broker, provision a MySQL database instance, and bind with it to get the configuration and credentials necessary for the application to utilize the database.

## Architecture

Service Catalog is built on the [Open Service Broker API](https://github.com/openservicebrokerapi/servicebroker) and consists of its own API Server and Controller, outside of the Kubernetes Core. It communicates with Service Brokers via the OSB API and serves as an intermediary for the Kubernetes API Server in order to negotiate the initial provisioning and return the information needed for the application to use the Service.

![Service Catalog Architecture](/images/docs/service-catalog-architecture.svg)

## Usage

*Broker Resources* identify available Service Brokers and can be added to a service catalog.

1. Get a list of services
1. Returns a list of services
1. Provision a new instance of a service
1. Creates a new instance and returns it
1. Bind to the instance
1. Returns provider-specific information, such as coordinates, credentials, configs, for Kubernetes to access service
1. Cleanup - delete binding, delete instance

Credentials are delivered to users in normal Kubernetes secrets and contain information necessary to connect with the service instance.

## API Resources

* ServiceBroker - an external service broker
* ServiceClass - a service
* ServiceInstance - the instantiation of a service
* ServiceBinding - interface to use the service instance

[sample service brokers](https://github.com/openservicebrokerapi/servicebroker/blob/master/gettingStarted.md#sample-service-brokers)

{% endcapture %}


{% capture whatsnext %}
* Explore the [service-catalog](https://github.com/kubernetes-incubator/service-catalog) project.

{% endcapture %}


{% include templates/concept.md %}
