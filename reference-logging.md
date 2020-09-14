---
copyright:
  years: 2019, 2020
lastupdated: "2020-09-14"

subcollection: cloud-databases

keywords: database logs

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:note: .note} 
{:tip: .tip}

# Log Analysis Integration
{: #logging}

{{site.data.keyword.databases-for}} are integrated with [{{site.data.keyword.la_full}}](/docs/Log-Analysis-with-LogDNA), so you can view database logs.

This document covers the integration of Log Analysis with Cloud Databases, which includes {{site.data.keyword.databases-for-cassandra}},{{site.data.keyword.databases-for-elasticsearch}}, {{site.data.keyword.databases-for-enterprisedb}}, {{site.data.keyword.databases-for-etcd}}, {{site.data.keyword.databases-for-mongodb}}, {{site.data.keyword.databases-for-postgresql}}, {{site.data.keyword.databases-for-redis}}, and {{site.data.keyword.messages-for-rabbitmq}}.
{: .note}

Currently, {{site.data.keyword.la_full_notm}} integration is available for {{site.data.keyword.databases-for}} deployments according to the following table.

Deployment Region | Log Analysis Region 
----------|-----------
`us-south` | `us-south`
`jp-tok` | `jp-tok`
`eu-gb` | `eu-gb`
`osl01` | `eu-gb`
`seo01` | `jp-tok`
`che01` | `che01`
`eu-de` | `eu-de`
`au-syd` | `au-syd`
`us-east` | `us-east`
{: caption="Table 1. Log Analysis regions" caption-side="top"}

Logs from your deployments appear in a Log Analysis instance in the same region, except for `osl01`, and `seo01`. Deployments in `osl01` have their logs forwarded to `eu-gb`. Deployments in `seo01` have their logs forwarded to `jp-tok`. If you have deployments in multiple regions, you must set up Log Analysis in multiple regions. 

After September 15th 2020, IBM {{site.data.keyword.databases-for}} in the Chennai region will begin sending Platform logs from our databases to the Logging with LogDNA services in Chennai rather than Tokyo. This is to ensure database logs for Chennai remain in the Chennai region.
{: .note}

## Provisioning {{site.data.keyword.la_full_notm}}

Log information from your databases is automatically forwarded to {{site.data.keyword.la_full_notm}}, but to access it you must [provision a Log Analysis service](/docs/Log-Analysis-with-LogDNA?topic=Log-Analysis-with-LogDNAprovision) in your {{site.data.keyword.cloud_notm}} account and [configure the service to receive {{site.data.keyword.cloud_notm}} service logs](/docs/Log-Analysis-with-LogDNA?topic=Log-Analysis-with-LogDNAconfig_svc_logs).

This setting enables logs from **ALL** {{site.data.keyword.cloud_notm}} services on your account that have {{site.data.keyword.la_full_notm}} integration to send logs to your {{site.data.keyword.la_full_notm}} service. [A list of the integrated services is available](/docs/Log-Analysis-with-LogDNA?topic=Log-Analysis-with-LogDNAcloud_services#cloud_services).
{: .tip}

{{site.data.keyword.la_full_notm}} has a lite plan that is free to use, but it offers only streaming events. To take advantage of the tagging, export, retention, and other features, you need to use one of the [paid plans](/docs/Log-Analysis-with-LogDNA?topic=Log-Analysis-with-LogDNAservice_plans).

### HIPAA 

{{site.data.keyword.la_full_notm}} does not currently offer a HIPAA-compliant plan for the service. Also, use caution when you configure the platform service logs, since this setting can impact other services that require HIPAA compliance.

## Using {{site.data.keyword.la_full_notm}}

You can access {{site.data.keyword.la_full}} through the _Observability_ tab of your deployment's _Manage_ page. The **Manage Log Analysis** button links to the main list of all Log Analysis instances in your IBM Cloud account. Select the instance where you set your database logs to be forwarded. Click **View LogDNA** to view the logs.

Each log line can be expanded to a detailed view by clicking the arrow to the left of the timestamp. The expanded view has some handy, color-coded fields to help you parse your logs. 

- _Line Identifiers_
    - `Source` - the region the logs are being sent from
    - `App` - the ID of the application that sent the log. It is the last part of your deployment ID and CRN.

- _Tags_
    - `#` - all of the logs that come from a {{site.data.keyword.databases-for}} deployment are tagged with `ibm-cloud-databases`

- _Labels_
    - `database` - the database type that this log is from
    - `member` - the member ID for which node in the cluster generated the log
    - `CRN` - the full deployment ID of your deployment.

{{site.data.keyword.la_full_notm}} offers [searching](/docs/Log-Analysis-with-LogDNA?topic=Log-Analysis-with-LogDNAview_logs#view_logs_step6), [filtering](/docs/Log-Analysis-with-LogDNA?topic=Log-Analysis-with-LogDNAview_logs#view_logs_step5) to help you navigate your logs.

[Export](/docs/Log-Analysis-with-LogDNA?topic=Log-Analysis-with-LogDNAexport#export) and [archiving](/docs/Log-Analysis-with-LogDNA?topic=Log-Analysis-with-LogDNAarchiving#archiving) are available so you can customize retention (and cost) for your use-case.

To set up logging alerts, see [Working with Alerts](/docs/Log-Analysis-with-LogDNA?topic=Log-Analysis-with-LogDNAalerts#alerts)

For more information on features offered by {{site.data.keyword.la_full_notm}}, including integrating it with your other {{site.data.keyword.cloud_notm}} services, see [its full documentation](/docs/Log-Analysis-with-LogDNA).

## Internal Log Retention

Database logs for all {{site.data.keyword.databases-for}} deployments are kept internally for 30 days and then purged. If your {{site.data.keyword.la_full_notm}} plan is for a shorter period, logs are only accessible by you for the length of your plan. Regardless of the plan that you choose, all database logs are deleted after 30 days.