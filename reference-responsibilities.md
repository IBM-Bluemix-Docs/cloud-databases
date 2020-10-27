---

copyright:
  years: 2019, 2020
lastupdated: "2020-10-27"

subcollection: cloud-databases

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:codeblock: .codeblock}
{:note: .note}
{:tip: .tip}

# Responsibilities for {{site.data.keyword.databases-for}}
{: #responsibilities-cloud-databases}

Learn about the management responsibilities and terms and conditions that you have when you use {{site.data.keyword.cloud}} {{site.data.keyword.databases-for}}. For a high-level view of the service types in {{site.data.keyword.cloud_notm}} and the breakdown of responsibilities between the customer and {{site.data.keyword.IBM_notm}} for each type, see [Shared responsibilities for {{site.data.keyword.cloud_notm}} offerings](/docs/overview?topic=overview-shared-responsibilities).
{:shortdesc}

This document covers all the Cloud Databases, that include {{site.data.keyword.databases-for-cassandra}}, {{site.data.keyword.databases-for-elasticsearch}}, {{site.data.keyword.databases-for-enterprisedb}}, {{site.data.keyword.databases-for-etcd}}, {{site.data.keyword.databases-for-mongodb}}, {{site.data.keyword.databases-for-postgresql}}, {{site.data.keyword.databases-for-redis}}, and {{site.data.keyword.messages-for-rabbitmq}}.
{: .note}

Review the following sections for the specific responsibilities for you and for {{site.data.keyword.IBM_notm}} when you use {{site.data.keyword.databases-for}}. For the overall terms of use, see [{{site.data.keyword.cloud_notm}} Terms and Notices](/docs/overview/terms-of-use?topic=overview-terms).

## Incident and operations management
{: #incident-and-ops-responsibilities}

| Task | {{site.data.keyword.IBM_notm}} Responsibilities | Your Responsibilities |
|----------|-----------------------|--------|
|Monitoring| {{site.data.keyword.databases-for}} is responsible for hosting monitoring and health services. | The Customer is responsible for integrating with the [Monitoring service](/docs/Monitoring-with-Sysdig?topic=Monitoring-with-Sysdig-platform_metrics_enabling), [Activity Tracker](/docs/cloud-databases?topic=cloud-databases-activity-tracker), or [Logging service](/docs/cloud-databases?topic=cloud-databases-logging). |
|High Availability| {{site.data.keyword.databases-for}} is responsible for deploying databases across availability zones in a Multi-Zone Region (MZR), or across hosts in a Single-Zone Region (SZR), as well as storing backups in cross-region Cloud Object Store instances. {{site.data.keyword.databases-for}} provides replication, fail-over features, and infrastructure maintenance/updates. High availability varies based on each database type, refer to database-specific documentation for details. | The Customer is responsible for designing application logic to retry connections caused by temporary connection failures (during regular database maintenance and updates).|
|Database performance | {{site.data.keyword.databases-for}} is responsible for hosting and maintaining database infrastructure. | The Customer is responsible for the data model and performance, including tuning the data model, queries, and scaling the database appropriately for application needs. |
|Operating System | {{site.data.keyword.databases-for}} is responsible for hosting and maintaining database Operating System infrastructure. | The Customer is not responsible for, nor has access to, Operating System level activities. |
{: caption="Table 1. Responsibilities for incident and operations" caption-side="top"}

## Change management
{: #change-management-responsibilities}

| Task | {{site.data.keyword.IBM_notm}} Responsibilities | Your Responsibilities |
|----------|-----------------------|--------|
|Scaling| {{site.data.keyword.databases-for}} is responsible for scaling infrastructure to meet customer requests. | The Customer is responsible for choosing, monitoring, and scaling disk, memory, and CPU core allocation for their deployments by using the UI or API. If a database deployment runs out of disk space, it may go down and have to be restored from backup. |
|Version management | {{site.data.keyword.databases-for}} is responsible for maintaining minor versions and patches as described in the [version lifecycle policy](/docs/cloud-databases?topic=cloud-databases-versioning-policy). {{site.data.keyword.databases-for}} is also responsible for providing availability and tools for database major version upgrades. | The Customer is responsible for running major database version upgrades. The Customer is also responsible for monitoring for EOL announcements and moving off EOL versions before the end of support date announced by {{site.data.keyword.databases-for}} also described in the version lifecycle policy.|
{: caption="Table 2. Responsibilities for change management" caption-side="top"}

## Security and regulation compliance
{: #security-responsibilities}

| Task | {{site.data.keyword.IBM_notm}} Responsibilities | Your Responsibilities |
|----------|-----------------------|--------|
|Encryption| {{site.data.keyword.databases-for}} is responsible for the encryption of data on disk, in motion, and in backups. | The Customer is responsible for choosing and managing appropriate additional security features. If using Key Protect and Bring Your Own Key (BYOK), the customer is responsible for managing Key Protect authorizations and keys. |
|Security| {{site.data.keyword.databases-for}} is responsible for ensuring the security of data on disk and data in motion within our infrastructure. | The Customer is responsible for managing {{site.data.keyword.cloud_notm}} passwords and database passwords, and keeping passwords secure. The Customer is also responsible for configuring appropriate network security or isolation (for example, IP allowlists or private endpoints). |
{: caption="Table 4. Responsibilities for security and regulation compliance" caption-side="top"}

## Disaster recovery
{: #disaster-recovery-responsibilities}

| Task | {{site.data.keyword.IBM_notm}} Responsibilities | Your Responsibilities |
|----------|-----------------------|--------|
|Backups and restore| {{site.data.keyword.databases-for}} is responsible for taking automatic daily backups, monitoring the state of customer backups.| The Customer is responsible for restoration, timeliness, and validity of backups. More information can be found in the [Backup and Restore documentation](/docs/cloud-databases?topic=cloud-databases-dashboard-backups).|
|Read-only replicas, {{site.data.keyword.databases-for-postgresql}} and {{site.data.keyword.databases-for-enterprisedb}} ONLY| {{site.data.keyword.databases-for-postgresql}} and {{site.data.keyword.databases-for-enterprisedb}} are responsible for providing the capability of deploying read-only replicas across regions (except for replicating data into or outside of `eu-de`). | The Customer is responsible for provisioning, configuring, monitoring, and promoting read-only replicas. |
{: caption="Table 5. Responsibilities for disaster recovery" caption-side="top"}

