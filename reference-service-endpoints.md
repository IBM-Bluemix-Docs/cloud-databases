---
Copyright:
  years: 2019, 2020
lastupdated: "2020-08-04"

subcollection: cloud-databases

keywords: service endpoints, private endpoints, private networking

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:note: .note}
{:tip: .tip}

# Service Endpoints Integration
{: #service-endpoints}

All {{site.data.keyword.databases-for}} deployments offer integration with [{{site.data.keyword.cloud_notm}} Service Endpoints](/docs/account?topic=account-service-endpoints-overview#about). It gives you the ability to enable connections to your deployments from the public internet and over the {{site.data.keyword.cloud_notm}} Private network.

Service Endpoints are available in all {{site.data.keyword.cloud_notm}} Multi-Zone Regions and some Single-Zone Regions. If your deployments are in `Osl01`, you aren't able to use private endpoints. Deployments in all other regions are able to use Service Endpoints.

This document covers the integration of Key Protect with Cloud Databases, which includes {{site.data.keyword.databases-for-cassandra}}, {{site.data.keyword.databases-for-elasticsearch}}, {{site.data.keyword.databases-for-enterprisedb}}, {{site.data.keyword.databases-for-etcd}}, {{site.data.keyword.databases-for-mongodb}}, {{site.data.keyword.databases-for-postgresql}}, {{site.data.keyword.databases-for-redis}}, and {{site.data.keyword.messages-for-rabbitmq}}.
{: .note}

## Public Endpoints

Public endpoints provide a connection to your deployment on the public network. At provision time, a public endpoint is the default option for all deployments. Your environment needs to have internet access to connect to a deployment.

## Private Endpoints

A deployment with a service endpoint on the private network gets an endpoint that is not accessible from the public internet. All traffic is routed to hardware dedicated to {{site.data.keyword.databases-for}} deployments and remains on the {{site.data.keyword.cloud_notm}} Private network. All traffic to and from this endpoint is free and unmetered on the condition that the traffic remains in {{site.data.keyword.cloud_notm}}. After your environment has access to the {{site.data.keyword.cloud_notm}} Private network, an internet connection is not required to connect to your deployment.

## Enabling Service Endpoints

If you want to use connections over the public internet, you do not have to enable Service Endpoints on your {{site.data.keyword.cloud_notm}} account. If you want to enable private networking on your deployments, you need to follow the instructions in the Service Endpoint documentation under [Setting up service endpoints](/docs/account?topic=account-vrf-service-endpoint#cs_cli_install_steps).

Currently, enabling Service Endpoints on your account is a manual step that is handled by support ticket. After you complete the [request](/docs/account?topic=account-vrf-service-endpoint#cs_cli_install_steps), you can check on the status of the ticket by going to your [Support](https://cloud.ibm.com/unifiedsupport/cases/manage) page on {{site.data.keyword.cloud_notm}}

## Provisioning with Service Endpoints

To configure your deployment's endpoints on provision, use the _Endpoints_ field on the catalog page. Select from the available options:
- Public Network
- Private Network
- Both public and private network (not available for {{site.data.keyword.databases-for-mongodb}})

### Provisioning from the CLI and API

Service Endpoints are enabled through an optional parameter when you provision through the CLI and API. Provisioning is handled by the Resource Controller, and you pass the `service-endpoints` parameter one of the options `public`, `private`, or `public-and-private`. 
```
ibmcloud resource service-instance-create <service-name> --service-endpoints <endpoint-type>
```

For more information, see the [Provisioning](/docs/cloud-databases?topic=cloud-databases-provisioning) page.

{{site.data.keyword.databases-for}} deployments except {{site.data.keyword.databases-for-mongodb}} allow for both public and private networking to be enabled at the same time.
{: .tip}

## Changing Service Endpoints

After you have a deployment, it is possible to change your public and private service endpoints configuration, except for {{site.data.keyword.databases-for-mongodb}}. 

In the _Settings_ tab of your deployment's dashboard, there is a card for _Service Endpoints_. You can toggle which types of connections are available to your deployment.

You can use the [`ibmcloud resource service-instance-update`](/docs/cli?topic=cli-ibmcloud_commands_resource#ibmcloud_resource_service_instance_update) command in the CLI, specifying the endpoint with the `--service-endpoints` flag.
```
ibmcloud resource service-instance-update <service-name> --service-endpoints <endpoint-type>
```

Or you can use the [Resource Controller API](https://cloud.ibm.com/apidocs/resource-controller), with a `PATCH` request to the [/resource_instances/{id}](https://cloud.ibm.com/apidocs/resource-controller#update-a-resource-instance) endpoint.

Changing the type of endpoints available on your deployment does not cause any downtime from a database perspective. However, if you disable an endpoint that is being used by you or your applications, those connections are dropped.

## Credentials for Private Endpoints

You can use either public or private connection strings with any set of credentials you make on your deployment. By default, the connection strings for a set of credentials are filled with strings for connecting over a public endpoint. If you are using private endpoints, you can specify connection strings that contain the private endpoint be generated instead. 

When you create credentials in _Service Credentials_, use either the `{ "service-endpoints": "public" }` or the `{ "service-endpoints": "private" }` parameter to specify which endpoint gets filled into the connection strings. 

In the API, you can use the [`/deployments/{id}/users/{userid}/connections/{endpoint_type}`](https://{DomainName}/apidocs/cloud-databases-api#discover-connection-information-for-a-deployment-f-e81026) to retrieve connection strings for both public or private endpoints.

If you only have private endpoints on your deployments, then all new credentials have private endpoints in the connection strings.

## Connecting Through Private Endpoints
{: #private-endpoint-connections}

{{site.data.keyword.cloud}} Databases offer both private and public cloud service endpoints. If you want to run your application or access the end point from a browser that is not on the private network, you must take these additional steps: 
  
* Ensure your Cloud IaaS or SL account is [enabled for private endpoints](https://cloud.ibm.com/docs/account?topic=account-service-endpoints-overview).
* Create a virtual machine (VSI) that runs Linux
* Configure a user account with SSH access
* From your workstation, run `ssh -D 2345 user@vsi-host` This starts an SSH session and open a SOCKS proxy on port 2345 that forwards all traffic through the VSI
* Configure your browser or application to use a SOCKS5 proxy on `localhost:2345`
* Run your application or open the preferred private-endpoint in your browser (for example, a management UI).
