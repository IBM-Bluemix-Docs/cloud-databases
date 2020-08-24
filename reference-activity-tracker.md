---
copyright:
  years: 2019, 2020
lastupdated: "2020-08-05"

subcollection: cloud-databases

keywords: events, auditing

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:note: .note}
{:tip: .tip}

# Activity Tracker Integration
{: #activity-tracker}

{{site.data.keyword.cloud_notm}} Databases deployments are integrated with Activity Tracker events in [IBM Cloud Activity Tracker with LogDNA](/docs/Log-Analysis-with-LogDNA?topic=LogDNA-getting-started), so you can view service-level events.

This document covers the integration of Activity Tracker with Cloud Databases, which includes {{site.data.keyword.databases-for-cassandra}}, {{site.data.keyword.databases-for-elasticsearch}}, {{site.data.keyword.databases-for-enterprisedb}}, {{site.data.keyword.databases-for-etcd}}, {{site.data.keyword.databases-for-mongodb}}, {{site.data.keyword.databases-for-postgresql}}, {{site.data.keyword.databases-for-redis}}, and {{site.data.keyword.messages-for-rabbitmq}}.
{: .note}

Currently, Activity Tracker with LogDNA integration is available for {{site.data.keyword.databases-for}} deployments according to the following table. 

Deployment Region | Activity Tracker Region 
----------|-----------
`us-south` | `us-south`
`jp-tok` | `jp-tok`
`eu-gb` | `eu-gb`
`osl01` | `eu-gb`
`seo01` | `jp-tok`
`che01` | `jp-tok`
`eu-de` | `eu-de`
`au-syd` | `au-syd`
`us-east` | `us-east`
{: caption="Table 1. Activity Tracker regions" caption-side="top"}

Events from your deployments appear in an Activity Tracker instance in the same region, with the exception of `osl01`, `seo01`, and `che01`. Deployments in `osl01` have their events forwarded to `eu-gb`. Deployments in `seo01` and `che01` have their events forwarded to `jp-tok`. If you have deployments in multiple regions, you have to set up the Activity Tracker in multiple regions. 

## Activity Tracker through LogDNA

Once you provision the service, events are automatically forwarded from all your {{site.data.keyword.databases-for}} deployments in the same region.

The service can be provisioned from [its catalog page](https://{DomainName}/catalog/ibm-cloud-activity-tracker-with-logdna) or from an existing [Observability Dashboard](https://cloud.ibm.com/observe/activitytracker).

The Activity Tracker with LogDNA service has a lite plan that is free to use, but it only offers streaming events. To take advantage of the tagging, export, retention, and other features, you need to use one of the [paid plans](/docs/Log-Analysis-with-LogDNA?topic=LogDNA-about#overview_pricing_plans).

### Using the LogDNA Activity Tracker

You can access Activity Tracker with LogDNA through the _Observability_ tab of your deployment's _Manage_ page. The **Manage Activity Tracker** button links to the main list of all Activity Tracker instances in your IBM Cloud account. Select the instance where you set your database logs to be forwarded. Click **View Activity Tracker** to view the events.

Once event activity is being forwarded to the service, each event can be expanded to a detailed view by clicking the arrow to the left of the timestamp.

The LogDNA service offers [searching](/docs/Log-Analysis-with-LogDNA?topic=LogDNA-view_logs#view_logs_step6), [filtering](/docs/Log-Analysis-with-LogDNA?topic=LogDNA-view_logs#view_logs_step5), and [export](/docs/Log-Analysis-with-LogDNA?topic=LogDNA-export#export) of events so you can customize retention for your use-case. You can also use it to configure [alerts](/docs/Log-Analysis-with-LogDNA?topic=LogDNA-alerts).

## Event Fields

A description of the common fields for an Activity Tracker event is on the [event fields](/docs/Activity-Tracker-with-LogDNA?topic=logdnaat-event) page.

## List of Events

The table lists the events that get sent to Activity Tracker from {{site.data.keyword.databases-for}} deployments.

Action|Description
-------|-------
`<service_id>.backup-ondemand.create`|An on-demand backup of your deployment was created. If the backup failed, a "-failure" flag is included in the message.
`<service_id>.backup-scheduled.create`|A scheduled backup of your deployment was created. If the backup failed, a "-failure" flag is included in the message.
`<service_id>.user-password.update`|A user's password was updated. A "-failure" flag is included in the message if the attempt to update a user's password failed.
`<service_id>.user.create`|A user was created. A "-failure" flag is included in the message if the attempt to create a user failed.
`<service_id>.user.delete`|A user was deleted. A "-failure" flag is included in the message if the attempt to delete a user failed.
`<service_id>.backup.restore`|A restore from backup was created. If the attempted restore failed, a "-failure" flag is included in the message.
`<service_id>.resources.scale`|A scaling operation was performed. If the scaling operation failed, a "-failure" flag is included in the message.
`<service_id>.whitelisted-ips-list.update`|The allowlist was modified. A "-failure" flag is included in the message if the attempt to modify the allowlist failed.
`<service_id>.serviceendpoints.update`|A change has been made to the service endpoints configuration. If the operation failed, a "-failure" flag is included in the message.
`<service_id>.autoscaling.update`|An autoscaling configuration change or an autoscaling operation was performed. If an autoscaling operation was performed the message includes `autoscale resources for instance <deployment-id>`. If the autoscaling operation or configuration change failed, a "-failure" flag is included in the message.
{: caption="Table 2. List of Events and Event Descriptions" caption-side="top"}

The `service_id` field indicates the type of {{site.data.keyword.databases-for}} deployment. For example, `databases-for-postgresql` or `messages-for-rabbitmq`.
