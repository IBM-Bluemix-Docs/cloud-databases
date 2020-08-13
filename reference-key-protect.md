---
copyright:
  years: 2019, 2020
lastupdated: "2020-08-05"

subcollection: cloud-databases, bring you own key, byok, cryptoshredding

---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:codeblock: .codeblock}
{:pre: .pre}
{:screen: .screen}
{:tip: .tip}
{:note: .note}
{:important: .important}

# Key Protect Integration
{: #key-protect}

The data that you store in {{site.data.keyword.cloud}} Databases is encrypted by default by using randomly generated keys. If you need to control the encryption keys, you can Bring Your Own Key (BYOK) through [{{site.data.keyword.keymanagementservicelong_notm}}](/docs/key-protect?topic=key-protect-integrate-services), and  use one of your own keys to encrypt your databases and backups.

This document covers the integration of Key Protect with Cloud Databases, which includes {{site.data.keyword.databases-for-cassandra}},{{site.data.keyword.databases-for-elasticsearch}}, {{site.data.keyword.databases-for-enterprisedb}}, {{site.data.keyword.databases-for-etcd}}, {{site.data.keyword.databases-for-mongodb}}, {{site.data.keyword.databases-for-postgresql}}, {{site.data.keyword.databases-for-redis}}, and {{site.data.keyword.messages-for-rabbitmq}}.
{: .note}

To get started, you need [{{site.data.keyword.keymanagementserviceshort}}](https://{DomainName}/catalog/key-protect) provisioned on your {{site.data.keyword.cloud_notm}} account.

## Creating or adding a key in {{site.data.keyword.keymanagementserviceshort}}

Navigate to your instance of {{site.data.keyword.keymanagementserviceshort}} and [generate or enter a key](/docs/key-protect?topic=key-protect-getting-started-tutorial).

## Granting service authorization

Authorize {{site.data.keyword.keymanagementserviceshort}} for use with {{site.data.keyword.databases-for}} deployments:

1. Open your {{site.data.keyword.cloud_notm}} dashboard.
2. From the menu bar, click **Manage** &gt; **Access (IAM)**.
3. In the side navigation, click **Authorizations**.
4. Click **Create**.
5. In the **Source service** menu, select the service of the deployment. For example, **Databases for PostgreSQL** or **Messages for RabbitMQ**
6. In the **Source service instance** menu, select **All instances**.
7. In the **Target service** menu, select **Key Protect**.
8. Select or retain the default value **`Account`** as the resource group for the **Target Service**
9. In the Target service **Instance ID** menu, select the service instances to authorize.
10. Enable the **Reader** role.
11. To use "Bring your own key" (BYOK) for backups, Select the **Enable authorizations to be delegated** box in the **Authorize dependent services** section.  
12. Click **Authorize**.

If the service authorization is not present before provisioning your deployment with a key, the provision fails.

## Using the Key Protect Key

Once you grant your {{site.data.keyword.databases-for}} deployments permission to use your keys, you supply the [key name or CRN](/docs/key-protect?topic=key-protect-view-keys) when you provision a deployment. The deployment uses your encryption key to encrypt your data.

If provisioning from the catalog page, select the Key Protect instance and key from the dropdown menus.

In the CLI, use the `disk_encryption_key_crn` parameter in the parameters JSON object.
```
ibmcloud resource service-instance-create example-database <service-name> standard us-south \
-p \ '{
  "disk_encryption_key_crn": "crn:v1:<...>:key:<id>"
}'
```

In the API, use the `disk-encryption-key` parameter in the body of the request.
```
curl -X POST \
  https://resource-controller.cloud.ibm.com/v2/resource_instances \
  -H 'Authorization: Bearer <>' \
  -H 'Content-Type: application/json' \
    -d '{
    "name": "my-instance",
    "target": "bluemix-us-south",
    "resource_group": "5g9f447903254bb58972a2f3f5a4c711",
    "resource_plan_id": "databases-for-x-standard",
    "disk_encryption_key_crn": "crn:v1:<...>:key:<id>"
  }'
```

If you provision a deployment through the CLI or API, the key protect key needs to be identified by its full CRN, not just its ID. A key protect CRN is in the format `crn:v1:<...>:key:<id>`.
{: .tip}

## Key Rotation

Key Protect offers manual and automatic [key rotation](https://cloud.ibm.com/docs/key-protect?topic=key-protect-rotate-keys) and key rotation is supported by Cloud Databases deployments. When you rotate a key, the process initiates a _Syncing KMS state_ task, and your deployment is reencrypted with the new key. The task is displayed on the _Tasks_ panel on your deployment's _Overview_ and the associated Key Protect and Cloud Databases events are sent to Activity Tracker.

## Deleting the Deployment

If you delete a deployment that is protected with a Key Protect key, the deployment remains registered against the key for the duration of the soft-deletion period (up to 9 days). If you need to delete the key in the soft-deletion period, you have to [force delete](/docs/key-protect?topic=key-protect-delete-keys) the key. After the soft-deletion period the key can be deleted without the force. You can check the [association between the key and your deployment](/docs/key-protect?topic=key-protect-view-protected-resources) to determine when you can delete the key.

## Cryptoshredding
{ #cryptoshredding}

Cryptoshredding is a destructive action. Once the key is deleted your data is unrecoverable.
{: .important}

{{site.data.keyword.keymanagementserviceshort}} allows you to [initiate a force delete](/docs/key-protect?topic=key-protect-delete-keys) of a key that is in use by {{site.data.keyword.cloud}} services, including your {{site.data.keyword.databases-for}} deployments. This action is called cryptoshredding. Deleting a key that is in use on your deployment locks the disks containing your data and disables your deployment. You are still able to access the UI and some metadata such as security settings in the UI, CLI, and API but you are not able to access any of the databases or data contained within them. Key deletion is [sent to the LogDNA Activity Tracker](/docs/key-protect?topic=key-protect-at-events) as `kms.secrets.delete`.

## BYOK for backups

If you use Key Protect, you can also designate a key to encrypt the Cloud-Object Storage disk that holds your deployment's backups.

Things to Note - 
- BYOK for backups is only available in US regions `us-south` and `us-east`.
- Only keys in the `us-south` are durable to region failures. You have to use a key from `us-south`, regardless of where your deployment is, to ensure that your backups are available even in the event of a region failure.

### Granting the delegation authorization

In order to enable your deployment to use the Key Protect key, you need to [Enable authorization to be delegated](/docs/iam?topic=iam-serviceauth) when granting the service authorizations. If the delegation authorization is not present before provisioning your deployment with a key, the provision fails.

### Using the Key at Provision

Once the appropriate authorization and delegation is granted, you  you supply the [key name or CRN](/docs/key-protect?topic=key-protect-view-keys) when you provision a deployment.

In the CLI, use the `backup_encryption_key_crn` parameter in the parameters JSON object.
```
ibmcloud resource service-instance-create example-database <service-name> standard us-south \
-p \ '{
  "backup_encryption_key_crn": "crn:v1:<...>:key:<id>"
}'
```

In the API, use the `backup_encryption_key_crn` parameter in the body of the request.
```
curl -X POST \
  https://resource-controller.cloud.ibm.com/v2/resource_instances \
  -H 'Authorization: Bearer <>' \
  -H 'Content-Type: application/json' \
    -d '{
    "name": "my-instance",
    "target": "bluemix-us-south",
    "resource_group": "5g9f447903254bb58972a2f3f5a4c711",
    "resource_plan_id": "databases-for-x-standard",
    "backup_encryption_key_crn": "crn:v1:<...>:key:<id>"
  }'
```

After you have enabled delegation and provisioned your deployment, two entries appear in your _Authorizations_ in IAM. One is the entry for the deployment which lists its status as delegator. It is "User Created".

Role | Source | Target | Type
-----|-----|-----|-----
AuthorizationDelegator, Reader | `<cloud-databases>` service | Key Protect Service | User defined
{: caption="Table 1. Example delegator Key Protect Authorization " caption-side="top"}

And one for the COS bucket for its backups, where the deployment is the initiator.

Role | Source | Target | Type
-----|-----|-----|-----
Reader | Cloud Object Storage service | Key Protect Service | created by `<cloud-databases-crn>`
{: caption="Table 2. Example Key Protect Authorization for COS from Cloud Databases " caption-side="top"}

### Removing Keys

IAM/Key Protect does not stop you from removing the policy between the key and Cloud Object Storage (the second example), but doing so can make your backups unrestorable. To prevent this, if you delete the COS policy that governs the ability of Cloud Databases to use the key for COS, the policy will be recreated to continue backing up your deployment.

Be very careful when removing keys and authorizations. If you have multiple deployments that use the same keys it is possible to inadvertently destroy backups to **all** of those deployments by revoking the delegation authorization. If at all possible you should not use the same key for multiple deployment's backups.

If you want to shred the backups, you can delete the key. COS takes care of making sure the storage is un-readable and un-writeable. But any other deployments using that key for backups will have all their backups fail.

If you do require that the same key be used for multiple deployment's backups removing keys and authorizations can have the following side effects.
- If you delete just the Cloud Object Storage authorization (as seen in Table 2), then not only is the deployment shown as the creator affected, but any deployments that also use the same key are also affected. Those deployments can see temporary backup failures until the policy is automatically re-created. There should be no lasting effects, except for missing backups.
- If you delete just Cloud Databases delegator authorization, which is created by you the user (as seen in Table 1), nothing immediately breaks, because the second authorization is still in place. However, if the  Cloud Object Storage authorization is ever removed, it cannot be recreated, and can lead to multiple deployments that use the same key losing the ability to backup.
- If you delete both the Cloud Object Storage authorization **AND** the Cloud Databases delegator authorization, all deployments that use the same key will immediately not have the ability to backup and the correct authorizations will not be able to be recreated, effectively destroying the backups for all deployments that use that key.

Please use caution if you reuse keys.