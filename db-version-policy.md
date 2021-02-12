---
Copyright:
  years: 2018, 2020
lastupdated: "2020-12-02"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}


# Database Versioning Policy
{: #versioning-policy}

When you provision a {{site.data.keyword.databases-for}} deployment, you can choose from the versions currently available on {{site.data.keyword.cloud_notm}}. You can find the latest versions from the [catalog pages](https://cloud.ibm.com/catalog?category=databases), from the cloud databases cli plug-in command [`ibmcloud cdb deployables-show`](/docs/databases-cli-plugin?topic=databases-cli-plugin-cdb-reference#deployables-show), or from the cloud databases API [`/deployables`](https://cloud.ibm.com/apidocs/cloud-databases-api#get-all-deployable-databases) endpoint.

## Version Tags

**Preferred** - The recommended and default database version for all new deployments. It's the version of the database that is the most up-to-date and stable with regard to both database-level and service-level features.

**Preview** - A preview database version is released for a limited time to try available functions. Often it is the newest available version available from the database project maintainers in preparation for making it the "Preferred" version. While deployable, preview versions are not suitable for production, as they are excluded from service-level agreements and support. Also, there is no guarantee that a preview version becomes a production-level release. IBM reserves the right to ask a customer to delete a deployment that uses a preview version.

**Deprecated** - Old versions and versions nearing their end of life dates are marked as "Deprecated". Provisions and restores of deployments that run a deprecated version are still available and deployments that are running a deprecated version continue to be supported. However, you are encouraged to upgrade to the new "Preferred" version of the database as deprecated versions are eventually removed from {{site.data.keyword.cloud_notm}} and are no longer provisionable, restorable, or supported. 

**Untagged** - Untagged database versions are fully supported and deployable versions. They are usually slightly older than the current preferred version, but they are still supported by the database project maintainers. They continue to be supported on {{site.data.keyword.databases-for}} deployments until their deprecation is announced.

## Deprecation of Major Versions

{{site.data.keyword.IBM_notm}} {{site.data.keyword.databases-for}} tries to support a major version of a database for 3 years from its release. 

If a database version is deprecated or marked end of life by the open source project owners, {{site.data.keyword.IBM_notm}} {{site.data.keyword.databases-for}} takes steps to deprecate that version.

When a major version is deprecated, a six-month transition window is opened for current users of the deprecated version.

At the beginning of the period, we seek to contact users affected by the deprecation. During the six-month transition window, users are able to initiate an upgrade to a supported major version. Existing deployments continue to run as normal.

Restoration of existing databases into new deployments of the deprecated major version is available during the six-month deprecation, although we recommend upgrading to a non-deprecated major version as soon as possible.
{: .tip}

At the end of the transition window, deprecated major versions cannot be deployed on {site.data.keyword.IBM_notm}} {{site.data.keyword.databases-for}}. A backup of the deployment is taken and access to databases that are running a deprecated version is removed or automatically upgraded to the next major version. See table 1 for which databases are upgraded or removed. Regardless, the backup is available to be restored into a new supported database version.

For customers using databases that are automatically upgraded to the next major version in-place: these databases are instead disabled if the customer is still using deprecated features.
{: .note}

## Major versions defined

Database | Versioning Schema | Next Known End of Life Version and Date | End of Life procedure |
--------- | --------- | --------- | --------- |
DataStax | Major versions are the first number in a `major.minor.patch` version number. | v6.7, unplanned | Backup taken and access removed|  
Elasticsearch | Major versions are the first number in a `release.version.maintenance` version number. | v6.x, [April 2021](https://www.ibm.com/cloud/blog/announcements/databases-for-mongodb-36-end-of-life-in-april-2021) | Backup taken and access removed|  
EnterpriseDB | Major version is defined by the first number in the version number. | v12, December 2024 | Backup taken and access removed|  
etcd | Major versions are the first number in a `major.minor.patch` version number. | v3.3, unplanned | Backup taken and access removed|  
MongoDB | Major versions are the first two numbers in a `major.x.patch` version number. In cases where `x` is even, it is a stable release suitable for production. Even `x` versions are the only ones available on Cloud Databases. | v3.6, [April 2021](https://www.ibm.com/cloud/blog/announcements/databases-for-mongodb-36-end-of-life-in-april-2021) | Automatically upgraded in place to next Major version|
PostgreSQL* | Major version is defined by the first number in the version number. | v9.5, [February 2021](https://www.ibm.com/cloud/blog/announcements/postgresql-9-5-end-of-life) & v9.6, [November 2021](https://www.postgresql.org/support/versioning/)| Backup taken and access removed|  
Redis | Major versions are the first number in a `major.minor.patch` version number. | v4.0, unplanned | Backup taken and access removed|  
RabbitMQ | Major versions are the first two numbers in a `major.x.patch` version number. | [v3.8, unplanned](https://www.ibm.com/cloud/blog/announcements/retiring-messages-for-rabbitmq-3-7) | Backup taken and access removed|  
{: caption="Table 1. Major versions for {{site.data.keyword.databases-for}}" caption-side="top"}

*There was a change of versioning schema for PostgreSQL after version 9.6. Before and including version 9.6, a PostgreSQL major version was defined by the first two numbers in the version.
{: .note}

## Minor versions

{{site.data.keyword.cloud_notm}} is committed to providing secure, up-to-date versions of databases. As updates are released by database project maintainers, they are tested, evaluated, and released to {{site.data.keyword.databases-for}} deployments. Your deployment's minor version and patch updates are handled automatically and are not user configurable. 
