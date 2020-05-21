---

copyright:
  years: 2019
lastupdated: "2019-09-17"

subcollection: cloud-databases, backups

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Managing Backups
{: #dashboard-backups}

Backups for {{site.data.keyword.databases-for}} deployments are accessible from the _Backups_ tab of your deployment's dashboard. Some general information about backups,

- One backup is taken every day.
- Backups are available for 30 days. 
- Backups cannot be deleted. 
- If you delete your deployment, its backups are deleted automatically.
- Scheduling of the daily backup is not configurable.
- Backups are cross-regionally durable. Backups are stored across multiple regions, and are restorable to other regions.
- Backup storage is encrypted. If you need to manage the encryption keys, you can do so with the [Key Protect integration](/docs/cloud-databases?topic=cloud-databases-key-protect#byok-for-backups). Otherwise, they are encrypted with a key automatically generated for your deployment.

## Backups in the UI

The backup types have their respective tabs, either _On-demand_ or _Automatic_. Each backup is listed with its type and when the backup was taken. Click the timestamp to change its format between elapsed time, local time, and UTC. 

![List of Backups on the Backups tab](images/backups-list.png)

Click the backup to reveal information for that specific backup, including its full ID. For restore options, there is a **Restore** button or a pre-formatted CLI command. 

## Backups in the CLI

You can also access the list of backups and individual backup information from the {{site.data.keyword.databases-for}} CLI plugin and the {{site.data.keyword.databases-for}} API.

Use the [`cdb deployment-backups-list`](/docs/databases-cli-plugin?topic=databases-cli-plugin-cdb-reference#deployment-backups-list) command to view the list of all available backups for your deployment. To get the details about a specific backup, use [`cdb backup-show`](/docs/databases-cli-plugin?topic=databases-cli-plugin-cdb-reference#backup-show) command.

For example, to view the backups for a deployment named "example-deployment", use the following command.

```
ibmcloud cdb deployment-backups-list example-deployment
```

To see the details of one of the backups from the list, take the ID from the `ID` field of the `deployment-backups-list` response and use it with the `backup-show` command.

```
ibmcloud cdb backup-show crn:v1:staging:public:cloud-databases:us-south:a/6284014dd5b487c87a716f48aeeaf99f:3b4537bf-a585-4594-8262-2b1e24e2701e:backup:a3364821-d061-413f-a0df-6ba0e2951566
```
## Backups in the API

For backups information in the {{site.data.keyword.databases-for}} API, use the [`/deployments/{id}/backups`](https://cloud.ibm.com/apidocs/cloud-databases-api#get-currently-available-backups-from-a-deployment) endpoint to list the deployment's backups. Use the [`/backups/{backup_id}`](https://{DomainName}/apidocs/cloud-databases-api#get-information-about-a-backup) endpoint to get information about a specific backup.

## Taking an On-demand Backup

On-demand backups are useful if you plan to make major changes to your deployment like scaling or removing databases, tables, collections. It can also be useful if you need to backup on a schedule. On-demand backups are kept for 30 days. 

Deployments come with free backup storage equal to their total disk space. If your backup storage utilization is greater than total disk space, each gigabyte is charged at an overage $0.03/month. Backups are compressed, so even if you use on-demand backups, most deployments will not ever go over the allotted credit.
{: .tip}

To create a manual backup in the UI, visit the _Backups_ tab of your deployment then click **Back up now**. A message is displayed that a backup is in progress, and an on-demand backup is added to the list of available backups.

In the CLI, you trigger an on-demand backup with the [`cdb deployment-backup-now`](/docs/databases-cli-plugin?topic=databases-cli-plugin-cdb-reference#deployment-backup-now) command.
```
ibmcloud cdb deployment-backup-now example-deployment
```

In the API, sending a POST to the [`/deployments/{id}/backups`](https://cloud.ibm.com/apidocs/cloud-databases-api#initiate-an-on-demand-backup) endpoint triggers an on-demand backup.

## Restoring a Backup

Backups are restored to a new deployment. After the new deployment finishes provisioning, your data in the backup file is restored into the new deployment.

By default the new deployment is auto-sized to the same disk and memory allocation as the source deployment at the time of the backup you are restoring from. If you need to adjust the resources allocated to the new deployment, use the optional fields in the UI, CLI, or API to resize the new deployment. Be sure to allocate enough for your data and workload, if the deployment is not given enough resources the restore will fail.

It is very important that you do not delete the source deployment while the backup is restoring. You must wait until the new deployment is provisioned and the backup is restored before deleting the old deployment. Deleting a deployment also deletes its backups so not only will the restore fail, you may not be able to recover the backup either.
{: .tip}

### Restoring a Backup in the UI

To restore a backup to a new service instance,

1. Click in the corresponding row to expand the options for the backup that you want to restore.
2. Click the **Restore** button.
3. Use the dialog box to select from some available options. 
  - The new deployment is automatically named `<name>-restore-[timestamp]`, but you can rename it. 
  - You can also select the region where the new deployment is located. Cross-region restores are supported, with the exception of restoring a `eu-de` backup to another region.
  - You can choose the initial resource allocation, either to expand or shrink the resources on the new deployment. You can also enable or disable dedicated cores.
4. Click the **Restore** button. A "restore from backup started" message appears. Clicking on **Your new instance is available now.** takes you to your _Resources List_.

### Restoring a Backup in the CLI

The Resource Controller supports provisioning of database deployments, and provisioning and restoring are the responsibility of the Resource Controller CLI. Use the [`resource service-instance-create`](/docs/cli?topic=cloud-cli-ibmcloud_commands_resource#ibmcloud_resource_service_instance_create) command.

```
ibmcloud resource service-instance-create <SERVICE_INSTANCE_NAME> <service-id> standard <region> -p '{"backup_id":"BACKUP_ID"}'
```

Change the value of `SERVICE_INSTANCE_NAME` to the name you want for your new deployment. The `service-id` is the type of deployment, such as `databases-for-postgresql` or `messages-for-rabbitmq`. The `region` is where you want the new deployment to be located, which can be a different region from the source deployment. Cross-region restores are supported, with the exception of restoring a `eu-de` backup to another region. `BACKUP_ID` is the backup that you want to restore.

Optional parameters are available when restoring through the CLI. Use them if you need to customize resources, or use a Key Protect key for BYOK encryption on the new deployment.
```
ibmcloud resource service-instance-create <SERVICE_INSTANCE_NAME> <service-id> standard <region> -p
'{"backup_id":"BACKUP_ID","key_protect_key":"KEY_PROTECT_KEY_CRN", "members_disk_allocation_mb":"DESIRED_DISK_IN_MB", "members_memory_allocation_mb":"DESIRED_MEMORY_IN_MB", "members_cpu_allocation_count":"NUMBER_OF_CORES"}'
```

A pre-formatted command for a specific backup is available in detailed view of the backup on the _Backups_ tab of your deployment dashboard.
{: .tip}

### Restoring a Backup through the API

The Resource Controller supports provisioning of database deployments, and provisioning and restoring are the responsibility of the Resource Controller API. You need to complete [the necessary steps to use the resource controller API](/docs/cloud-databases?topic=cloud-databases-provisioning#provisioning-through-the-resource-controller-api) before you can use it to restore from a backup. 

Once you have all the information, the create request is a `POST` to the [`/resource_instances`](https://{DomainName}/apidocs/resource-controller#create-provision-a-new-resource-instance) endpoint.

```
curl -X POST \
  https://resource-controller.cloud.ibm.com/v2/resource_instances \
  -H 'Authorization: Bearer <>' \
  -H 'Content-Type: application/json' \
    -d '{
    "name": "<SERVICE_INSTANCE_NAME>",
    "target": "<region>",
    "resource_group": "<your-resource-group>",
    "resource_plan_id": "<service-id>",
    "parameters":{
      "backup_id": "<backup_id>"
    }
  }'
```
The parameters `name`, `target`, `resource_group`, and `resource_plan_id` are all required, and `backup_id` is the backup you want to restore. The `target` is the region where you want the new deployment to be located, which can be a different region from the source deployment. Cross-region restores are supported, with the exception of restoring a `eu-de` backup to another region.

If you need to adjust resources or use a Key Protect key, add the optional parameters `key_protect_key`, `members_disk_allocation_mb`, `members_memory_allocation_mb`, and/or `members_cpu_allocation_count`, and their desired values to the body of the request.

## Backups and Restoration

* {{site.data.keyword.cloud_notm}} Databases is not responsible for restoration, timeliness, or validity of said backups.
* Actions that you take as a user can compromise the integrity of backups, such as under-allocating memory and disk. Users can monitor that backups were performed successfully via the API, and periodically restore a backup to ensure validity and integrity. Users can retrieve the most recent scheduled backup details from the [Cloud Databases CLI plug-in](#backups-in-the-cli) and the [Cloud Databases API](#backups-in-the-api).
* As a managed service, {{site.data.keyword.cloud_notm}} Databases monitors the state of your backups and can attempt to remediate when possible. If you encounter issues you cannot recover from, you can contact support for additional help.
