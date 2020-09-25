---

copyright:
  years: 2020
lastupdated: "2020-09-25"

subcollection: cloud-databases

keywords: HA for cloud-databases, DR for cloud-databases, high availability for cloud-databases, disaster recovery for cloud-databases, failover for cloud-databases

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:table: .aria-labeledby="caption"}
{:codeblock: .codeblock}
{:tip: .tip}
{:download: .download}

# Understanding high availability and disaster recovery for {{site.data.keyword.databases-for}}
{: #ha-dr}

This document covers all the {{site.data.keyword.cloud}} Databases, which include {{site.data.keyword.databases-for-cassandra}}, {{site.data.keyword.databases-for-elasticsearch}}, {{site.data.keyword.databases-for-enterprisedb}}, {{site.data.keyword.databases-for-etcd}}, {{site.data.keyword.databases-for-mongodb}}, {{site.data.keyword.databases-for-postgresql}}, {{site.data.keyword.databases-for-redis}}, and {{site.data.keyword.messages-for-rabbitmq}}. 
{: .note}

{{site.data.keyword.cloud}} Databases instances are deployed in either a multi-zone region (for example, Dallas, Frankfurt, London, Sydney, Tokyo, and Washington), or a single zone region (for example, Oslo, Seoul, and Chennai).  Each instance is deployed in a highly-available configuration.  That is, data is replicated by each database onto one or more servers making the data highly available during normal operations.

- In multi-zone regions, database members are distributed across different data centers, or zones.  
- In single-zone regions, database members are distributed across different hosts.

In the case of a single zone failure in a multi-zone region or a hardware failure in any region, your data is still accessible as it is replicated onto other fully functioning database servers. Such issues will be addressed by {{site.data.keyword.cloud}} Specialists in place. 

You may consult your {{site.data.keyword.databases-for}} documentation for more details on how your specific database replicates data among each of its members.

In addition to the high-availability configuration, your data is snapshotted and backed up daily by the {{site.data.keyword.cloud}} Databases platform and stored in [cross-region Cloud Object Storage buckets](https://cloud.ibm.com/docs/cloud-object-storage?topic=cloud-object-storage-endpoints#endpoints-geo). 

In the case of a complete region failure, the database servers in the region may not be accessible, but the backup data will remain available. You can initiate a restore from these backups into an available region from the service management console. Consult your {{site.data.keyword.databases-for}} backups page for additional details. 

It is your responsibility to [create a new service instance](/docs/cloud-databases?topic=provisioning) in which to restore to once the {{site.data.keyword.cloud}} Databases platform has been restored. You are also responsible for testing the validity and restore time of your backups. For more information on your responsibilities, see [Disaster recovery](/docs/cloud-databases?topic=cloud-databases-responsibilities-cloud-databases#disaster-recovery-responsibilities) in the *Responsibilities for Cloud Databases* page.

## Application-level high availability

Applications that communicate over networks and cloud services are subject to transient connection failures. You want to design your applications to retry connections when errors are caused by a temporary loss in connectivity to your deployment or to {{site.data.keyword.cloud_notm}}.

Because {{site.data.keyword.databases-for}} is a managed service, regular updates and database maintenance occurs as part of normal operations. This can occasionally cause short intervals where your database is unavailable.

Your applications have to be designed to handle temporary interruptions to the database, implement error handling for failed database commands, and implement retry logic to recover from a temporary interruption.

Several minutes of database unavailability or connection interruptions are not expected. Open a [support ticket](https://cloud.ibm.com/unifiedsupport/cases/add) with details if you have time periods longer than a minute with no connectivity so we can investigate.

If you have deployments in more than one region, you have to provision Sysdig and enable platform metrics in each region. For more information on enabling Sysdig, see your {{site.data.keyword.databases-for}} deployment's *Monitoring with Sysdig* page

## SLAs
See [How do I ensure zero downtime?](/docs/overview?topic=overview-zero-downtime#zero-downtime) to learn more about the high availability and disaster recovery standards in {{site.data.keyword.cloud_notm}}.

All {{site.data.keyword.cloud}} Databases general availability (GA) offerings conform to the {{site.data.keyword.cloud}} [Service Level Agreement](/docs/overview?topic=overview-slas) (SLA) terms.

For more information on your responsibilities, see the [Responsibilities for Cloud Databases](/docs/cloud-databases?topic=cloud-databases-responsibilities-cloud-databases) page.

